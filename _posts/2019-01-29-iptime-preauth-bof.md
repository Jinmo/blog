---
layout: post
title: iptime 10.00.2 preauth vulnerability
date: 2018-01-29 16:39:00.000000000 +09:00
author: jinmo
---

2017년 8월 31일 제보한 취약점입니다.

## 취약점 설명

ipTIME 공유기를 쓰신다면, 192.168.0.1 에 들어가보세요.

![iptime admin]({{ site.baseurl }}/assets/images/iptime-admin.png)

이런 화면이 뜹니다. 만약 각 입력값이 C언어로 짠 프로그램에서 처리가 되고, 해당 프로그램의 소스코드가 아래와 같으면 어떨까요?

```c
int main() {
    char id[0x100], pw[0x100];
    strcpy(id, get_value("id"));
    strcpy(id, get_value("pw"));
    if(check(id, pw)) {
        login();
    }
    return 0;
}
```

ipTIME을 포함한 꽤나 많은 공유기들은 이러한 방식을 택하고 있습니다. 보통 이를 CGI 방식이라고 칭합니다.

2014년에는 제가 쓰던 공유기의 모든 입력 필드가 위와 같은 취약점을 가지고 있었습니다. 하지만 꽤 많은 취약점 신고가 이루어졌고, 요즘은 위의 코딩 패턴 자체를 방지하도록 `get_value` 같은 함수에서 복사할 최대 길이를 지정하도록 바꾸었습니다.

```c
void get_value(char *dst, char *name, size_t size) {
	if(!size) return;
    memset(dst, 0, size);
    strncpy(dst, __get_value_internal(name), size - 1);
}
```

바람직한 변화죠. 하지만 2018년에 또 다른 로그인 화면이 생깁니다. 바로 **모바일 전용 로그인** 화면입니다.

![iptime mobile login]({{ site.baseurl }}/assets/images/iptime-mlogin.png)

모바일 로그인 화면에서는 로그인 실패 시 ID정보를 다시 보여주기 위해 GET 인자로 전달하는데, Base64 인코딩을 해서 전송합니다. `info`라는 GET 인자로 전송하죠. 요렇게요.

![iptime-mobile]({{ site.baseurl }}/assets/images/iptime-mlogin2.png)

m_login.cgi는 ELF파일인데요, info= 뒤의 base64 문자열을 아래와 같이 풀어 처리해줍니다.

```c
int main() {
	char buf[0x400];
    Base64decode(buf, get_pvalue("info"));
    // ...
    printf("<input type=text name=id value=%s", buf);
}
```

Base64decode 함수에 들어간 것은 무엇일까요? 출력 버퍼와 입력 버퍼입니다. 사이즈가 없군요. 잘 찾아보니 같은 루틴이 있었는데, 원본에는 디코딩 후 길이를 구해주는 루틴이 같이 있었습니다.

```c
int Base64decode_len(const char *bufcoded)
{
    int nbytesdecoded;
    register const unsigned char *bufin;
    register int nprbytes;

    bufin = (const unsigned char *) bufcoded;
    while (pr2six[*(bufin++)] <= 63);

    nprbytes = (bufin - (const unsigned char *) bufcoded) - 1;
    nbytesdecoded = ((nprbytes + 3) / 4) * 3;

    return nbytesdecoded + 1;
}

int Base64decode(char *bufplain, const char *bufcoded)
{
    int nbytesdecoded;
    register const unsigned char *bufin;
    register unsigned char *bufout;
    register int nprbytes;

	...
}

```

하지만 ipTIME에는 해당 함수가 쓰이지 않았고, 디코딩 루틴에서도 출력 버퍼의 크기를 고려하지 않습니다. 따라서 지역 변수, 즉 스택 상의 buffer overflow 취약점이 일어났고, 임베디드 장비들 중 stack protector (canary)가 쓰인 장비는 본 적이 없기 때문에, 바로 ROP를 할 수 있었습니다.

Base64 디코딩은 strcpy와는 달리 널바이트도 넣을 수 있으니 바이너리의 가젯들을 이용하여 원하는 커맨드를 실행해 줄 수 있어요. ROP로 어떤 루틴을 실행해줄까요?

## Return-oriented-programming: ROP

스택에서 버퍼가 넘친다면 어떤 것을 덮어 쓸 수 있을까요? 스택에는 지역 변수 말고도, 함수가 끝난 후 어느 주소로 점프할 지, 어느 레지스터를 복구해줄지와 같은 정보들이 있습니다.

```
----------------------------------------------
| buf[0x400] ... | $s0 | $s1 | $s2 | $ra | ...
----------------------------------------------
```

버퍼를 넘어서 스택에 저장된 $ra값을 덮으면, 함수 에필로그에서 $ra를 $pc에 불러오면서 부모 함수로 가요. 이를 원래 값이 아닌 저희가 원하는 프로그램 내 주소로 바꿔주면, 프로그램의 흐름을 마음대로 바꿀 수 있겠죠. 이해를 돕기 위해 함수의 끝부분을 보도록 합시다. main 함수의 끝입니다.

```
# int main(int argc, char *argv[], char *envp)

main:
# $sN, $ra 레지스터 저장
sw $s0, 0x530($sp)
sw $ra, 0x540($sp)
sub $sp, $sp, 0x550

...

# $sN, $ra 레지스터 복구
lw $s0, 0x530($sp)
lw $ra, 0x540($sp)
add $sp, $sp, 0x550
# $ra가 가리키는 주소로 점프!
jr $ra

```

스택의 위치가 함수 이전, 즉 $ra 다음에 있는 것처럼 프로그램은 계속해서 실행이 되겠죠.

가령 MIPS CPU에서는 보통 $a0 레지스터에 C언어상의 첫 번째 인자가 들어갑니다. 이걸 "sh"라는 문자열의 메모리 상 위치로 바꿔줍시다. $ra는 system으로 바꿔주면 되겠죠. system("sh")를 호출해주게 됩니다.

하지만 이 함수에서는 $a0을 스택에서 불러와주는 루틴이 없군요. 어찌어찌 찾아서 엮어주면 됩니다. 즉 함수들의  `return` 주변을 엮어서 실행하는 `Return-oriented-programming`, ROP의 시작입니다.

첫 번째 ra는, 아래의 루틴입니다.

```asm
.text:00014A64                 move    $a0, $s0                ; $a0 = $s0
.text:00014A68                 lw      $a2, 0x58+var_2C($sp)   ; $a2 = var_2C
.text:00014A6C                 move    $a3, $v0                ; $a3 = $v0
.text:00014A70                 move    $t9, $s6                ; $t9 = $s6
.text:00014A74                 jalr    $t9                     ; $t9($a0, $a1, ...)
.text:00014A78                 move    $a1, $s7                ; $a1 = $s7
```

MIPS에서는 파이프라인 효율성을 위해 delay slot이라는 개념이 있지요. 분기문이 있다 해도 그 다음 명령어가 CPU에 불러와진 뒤, 실행을 해주는 것입니다. 저는 컴퓨터 구조 시간 때 배웠습니다.

지금은 $t9 = $s6, 그리고 $a0 = $s0이라는 것이 중요한데요, 즉 $s~로 시작하는 레지스터들은 컴파일러에서 어떤 함수가 호출이 된 후에도 유지를 시켜주도록 만들어줍니다. $v0이 return 값이니 유지가 되지 않는 것처럼요.

함수 끝부분에서 $s6을 불러와주는 함수들은 많습니다. 아래와 같이 엮으면 되겠죠.

```
| buf: AAAAAAAA... | $s0 | $s1 | $s2 ...
... | $ra: $s0~$s6을 불러오는 함수 에필로그
... | $s0=&"/bin/sh"
... | $s6=&system(const char *cmd)
... | 0x00014A64
```

그러면 저희가 자주 쓰는 UNIX 쉘이 시작됩니다. CGI에서 stdin은 POST 데이터 값으로 정해져있기 때문에 (정확히는 그렇게 구현되어 있기 때문에) POST 데이터로 원하는 커맨드를 호출하면 HTTP 데몬을 통해 출력값이 나오게 됩니다.

## 결론

인터넷에는 종종 ipTIME의 관리자 인터페이스가 노출되어있습니다. 암호가 걸려있어도 펌웨어 업그레이드를 안 하면 큰일나겠군요. 공유기는 인터넷과 컴퓨터 중간에 놓여있기 때문에, 인터넷이라는 것을 컴퓨터에게 속일 수 있습니다.

ipTIME을 업그레이드 하려면 관리자 설정의 맨 밑에 있는 업그레이드 설정을 눌러주시면 됩니다. 요즘 펌웨어는 자동으로 업그레이드까지 해주더군요.

물려있는 기기들이 뭐뭐 있는지 보시고, [업그레이드 버튼](https://iptime.com/iptime/?page_id=67&uid=16243&mod=document)을 망설임 없이 눌러주세요!