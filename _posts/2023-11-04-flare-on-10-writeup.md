---
layout: post
title: 그누보드5 <5.2.8 검색 컬럼 취약점
date: 2023-11-04 00:00:00.000000000 +09:00
author: jinmo
---

Flare-on 10 writeups. You can read writeups for challenge details; I tried to minimize the content and focus on which tricks/mindsets helped me solve them fast. I didn't know there was rickroll midi file in one of the challenges. Oh my god.

## #1. X

Let's start with the first challenge: X.

![](../assets/images/x1.png)

Familiar filenames like CSharp there, so it seems like a .NET application, so let's use [ILSpy](https://github.com/icsharpcode/ILSpy). Usually, .exe is just a wrapper for launching .dll in .NET apps, so let's just go to X.dll.

![](../assets/images/x2.png)

Oh, "_successTexture"? Let's see which part of program uses it; We can click "Analyze" menu from ILSpy which shows a cross-reference to it.

![](../assets/images/x3.png)

Let's see _currentTexture.

```c#
private Texture2D _currentTexture {
...
		if (_state == BackgroundStates.Success)
			return _successTexture;
...
}
```

So, _currentTexture shows it if the "state" is success. Who sets the state? Click "Analyze" on _state again.

![](../assets/images/x4.png)

`Game1._lockButton_Click`?

![Alt text](../assets/images/x5.png)

Yey, `glorified_captcha@flare-on.com`; **Analyze** menu was quite useful here.
