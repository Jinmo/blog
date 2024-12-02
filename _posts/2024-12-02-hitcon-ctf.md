---
layout: post
title: '무제: 재밌는 CTF 컨셉들'
date: 2024-12-02 22:00:00.000000000 +09:00
author: jinmo
tags:
  - ctf
---

펑리수라는 과자를 먹다가 문득 HITCON CTF 본선때의 경험이 떠오르더라구요.

## HITCON CTF 2017: Capture the Food..?!

[HITCON CTF](https://ctf.hitcon.org/)는 매년 대만의 HITCON (Hack in Tawian Conference) 컨퍼런스에서 주최하는 CTF 해킹대회입니다.
저는 2015년부터 4년정도 참여했었고, 팀원들 덕분에 좋은 성적을 낼 수 있었습니다. 3연속 1등이라니, 세상에.
다시 말씀드리지만 팀원들에게 많이 배웠었습니다. Cykorkinesis 팀으로 출전했었는데, lokihardt, hellsonic, setuid0 그리고 제가 있던 팀입니다.

![Capture the food]({{ site.baseurl }}/assets/images/hitcon-food.jpg){: width="500" }

본론으로 들어가서, 타이페이 국제 컨벤션 센터에서 진행되었던
2017년 HITCON CTF에서는 Capture The Food 특집으로 본선이 진행되어서,
한 문제를 풀 때마다 주최측이 준비한 음식이 각 팀 테이블로 배달이 되었습니다. 요리사 복장을 하신 분이 와서,
아마 접시를 테이블에 올려놨었습니다. 소룡포 만두가 제일 맛있더라구요.
문제를 못 푼 팀은 음식을 못 먹는 잔인한 상황이였지만, 다행히도 저희 팀은 빠짐없이 먹었습니다 🤣.

뜬금없지만 이 때 풀었던 Rust기반의 웹서버 문제도 기억나네요.
바이너리를 분석하다보니 데몬을 내려버릴 수 있는 DoS 취약점을 의도치 않게 찾았었고,
점수 산정 공식이 "다운된 팀은 다른 팀에게 1점씩 분배"였기 때문에 저희 제외 모든 팀의 서비스를 다운시켜 점수를 꽤 많이 (N^2) 얻었었습니다.
해당 익스플로잇을 쏘기 전 운영진에게 "로지컬 DoS로 데몬을 다운시켜도 되는가" 물어봤을때가 짜릿한 경험이였습니다.

사실 이 글을 쓰게 된 계기가 이 때 주최측에서 펑리수를 참가자들에게 선물해줬던 기억이 나서.. ㅎㅎ
파인애플 잼이 들어간 빵 형태의 과자인데, 대만 면세점 대표 인기 상품입니다. 맛있으니 추천합니다.


## HITCON CTF 2015: 스타워즈 컨셉

![HITCON CTF 2015]({{ site.baseurl }}/assets/images/hitcon_2015.jpg){: width="500" }

HITCON CTF의 2015년 본선에는 LED등으로 만들어진 광선검이 각 테이블에 고정되어있었던 기억이 나네요.
대회 시작과 함께 빛이 났었던 것 같고, 문제를 처음으로 풀 때마다 굉장히 요란한 소리가 났었습니다.
하도 소리가 커서 중간에 자리를 좀 조정했었던.. ㅋㅋ

여담이지만 이때 팀 중 하나가 대회 끝나기 몇 분 전 인프라를 해킹해서 뭔가 재밌는 일이 벌어졌었는데.. 정확히는 기억이 안나네요.
이래서 미리미리 써둬야 합니다. 😅
HITCON은 대회의 모습을 담은 짤막한 동영상을 유튜브에도 올리기 때문에, 관심있으신 분들은 가서 보셔도 좋을 것 같습니다.
[(링크)](https://www.youtube.com/watch?v=CaDIi84JA7g)

## Real World CTF 2018

중국 정저우 시의 굉장히 큰 공간에서 대회를 진행했던 기억이 납니다.
이정도로 큰 곳은 작년에 갔던 Black Hat MEA CTF 본선밖에 없었는데, 거기는 100팀이 갔어서.. ㅋㅋㅋ

<iframe width="928" height="522" src="https://www.youtube.com/embed/2S_TXaGYD8E?start=477" title="Going to Chinese Hacking Competition - Real World CTF Finals" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

이 때 재밌었던 것은, 몇몇 문제들이 실제 제품을 해킹하는 것이였는데,
시연 기회가 3번만 있었고, 단상 위에 올라가서 시연을 해야했었습니다.
이 대회는 LiveOverflow가 유튜브에 이미 맛있는 영상을 올려놨기 때문에 위의 동영상으로 대체하겠습니다.
2분 30초에 건물이, 7분 57초에 시연 영상이 나옵니다.

시연을 하는 해킹대회라 한다면.. 요즘은 1:1 해킹 배틀을 유튜브로 생중계하는 DEFCON 본선의 LiveCTF라던지,
아니면 Pwn2Own 등 취약점 시연 대회들이 비슷한 분위기를 가질 것 같습니다.
아마 Pwn2Own은 테슬라를 해킹하면 실제로 줬던(!) 것으로 기억하네요. 전파, 자동차, 그리고 약간의 브라우저 해킹이 들어갔던 것으로 기억하니 참고해보셔도 좋을 것 같습니다.

## Kaspersky Industrial CTF 2017

카스퍼스키는 러시아의 보안 회사인데, 특이하게도 이 대회는 중국의 GeekPwn 2017과 함께 상하이에서 진행되었습니다.
상하이는 신기하게도 휴대폰에서 구글이 들어가졌던 기억이 나네요.

![kaspersky ctf]({{ site.baseurl }}/assets/images/kaspersky-ctf.jpg){: width="500" }

대회 이름은 아마 해킹대회 컨셉이 정유소, 즉 산업현장에서 쓰이는 ICS, PLC 등을 해킹하는 컨셉이여서 그랬던 것 같습니다.
단계별로 시스템을 해킹해가면서 최종적으로는 PLC까지 해킹하는 그림이였는데, 아쉽게도 마지막 단계로 가기 전에 대회가 끝나서 PLC를 해킹해보지는 못했습니다.
비슷한 주제로, 라스베가스의 DEFCON에서도 NSHC가 매년 [Red Alert ICS CTF](https://x.com/icsctf)를 운영하고 있죠.

## 마치며

그래서 이 글을 왜 쓰게 된 걸까요?
대학교 때 꽤 많은 CTF에 참여했었는데, 많이 배웠었고, 좋은 경험이였습니다.
재밌는 기억들이 가끔씩 떠오르다보니 잊어버리기 전에 써야겠다 싶었습니다.
