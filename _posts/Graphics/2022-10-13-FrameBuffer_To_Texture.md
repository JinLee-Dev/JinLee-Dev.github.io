---
layout: single
title:  "Frame Buffer to texture 적용기"
category: Graphics
tags: [Graphics, OpenGL]
date: 2022-10-13
comments : true
---

Post-Processing 개념을 적용해야 하는 기능을 받게 되었고, 적용하기 위해 공부한 내용 + 시행착오를 적어보려고 합니다.

## Frame Buffer
Frame Buffer는 Rendering시 사용될 수 있는 영역, Buffer들의 집합을 의미합니다.
Rendering 영역은 실제 렌더링 될 때 사용되는 Default Frame Buffer가 될 수 있고, User가 요청해서 임의로 생성하는 Frame Buffer Object(FBO) 가 될 수도 있습니다.

## FBO
User가 정의한, 실제로 그려지지도 않는 이 FBO는 아래와 같은 Case에서 많이 사용하고 있습니다.
* 이미 그렸던 Scene을 Texture의 형태로 다시 그려야 할 때
* 안티앨리어싱
* Post-Processing
