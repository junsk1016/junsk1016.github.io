---
layout: single
title: "IDE에서 matplotlib plt 창 띄우기"
categories:
  - Spyder
classes: wide
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - Spyder
---

###### 스파이더에서 plot 창을 따로 띄우는 방법에 대해 포스팅하겠습니다.

평소엔 신경을 쓰지 않고 사용하였지만 3d plot을 볼 기회가 있어서 봤는데
기존으로는 회전이 되지 않더라구요.

그래서 독립된 창에 띄우는 방법을 정리하려고 합니다.

1. 설정으로 바꾸기

Tools -> Preferences -> IPython console -> Graphics에서 Backend 기본값으로 설정되어있는 `inline`을 `Automatic`으로 변경후 Spyder 재시작

2. command로 바꾸기

IPython console에 다음과 같이 입력
```
%matplotlib qt
```

원래대로 돌아가려면 다음과 같이 입력
```
%matplotlib inline
```

3. Script마다 다르게 하고 싶을 때
Script 파일에 다음 코드를 사용
```python
from IPython import get_ipython
get_ipython().run_line_magic('matplotlib','qt')
