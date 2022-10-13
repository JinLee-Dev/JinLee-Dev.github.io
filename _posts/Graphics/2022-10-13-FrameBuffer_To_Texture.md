---
layout: single
title:  "Frame Buffer to texture"
category: Graphics
tags: [Graphics, OpenGL]
date: 2022-10-13
comments : true
---

Post-Processing 개념을 적용하여야 하는 업무를 받게 되었고, 적용하기 위해 공부한 내용 + 시행착오를 적어보려고 합니다.

## Frame Buffer
Frame Buffer는 Rendering시 사용될 수 있는 영역, Buffer들의 집합을 의미합니다.
Rendering 영역은 실제 렌더링 될 때 사용되는 Default Frame Buffer가 될 수 있고, User가 요청해서 임의로 생성하는 Frame Buffer Object(FBO) 가 될 수도 있습니다.

## FBO
User가 정의한, 실제로 그려지지도 않는 이 FBO는 아래와 같은 Case에서 많이 사용하고 있습니다.
* 이미 그렸던 Scene을 Texture의 형태로 다시 그려야 할 때
* 안티앨리어싱
* Post-Processing

## FBO를 활용한 Post Processing
기능 구현을 위해선 Object를 그리기 전 상태의 Color값과 Depth 정보가 필요했었습니다.
두 값을 추출하기 위해 아래와 같은 방법으로 OpenGL Function을 호출하였습니다.

<br>

우선 새로운 FBO를 생성해줍니다.
```cpp
//  새 FBO를 생성하여 Binding
glGenFramebuffers(1, &fbo);    
glBindFramebuffer(GL_DRAW_FRAMEBUFFER, fbo);  
```

그리고 Main Buffer에서 Rendering시 사용할 새로운 Texture를 선언 후, FrameBuffer에 붙여 줍니다.
```cpp
// Color Texture, Depth Texture 생성
glGenTextures(1, &colorTexture);
glGenTextures(1, &depthTexture);

// Color Texture 설정
glBindTexture(GL_TEXTURE_2D, colorTexture);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, Width, Height, 0, GL_RGB, GL_FLOAT, NULL);
// Color Texture에 FrameBuffer를 붙인다.
glFramebufferTexture2D(GL_DRAW_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, colorTexture, 0); 

// Depth Texture 설정
glBindTexture(GL_TEXTURE_2D, depthTexture);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexImage2D(GL_TEXTURE_2D, 0, GL_DEPTH_COMPONENT24, width, height, 0, GL_DEPTH_COMPONENT, GL_UNSIGNED_INT, NULL);
glBindTexture(GL_TEXTURE_2D, 0);
// Depth Texture에 FrameBuffer를 붙인다.
glFramebufferTexture2D(GL_DRAW_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_TEXTURE_2D, depthTexture, 0);
```

이 때 Frame Buffer 및 Attach된 Frame Buffer Status가 정상적으로 적용되었는지 확인을 해야 합니다.
Texture와 Frame Buffer에 문제가 있을 경우, Status가 정상적이지 않은 값으로 Return 됩니다.

```cpp
GLenum Status = glCheckFramebufferStatus(GL_FRAMEBUFFER);
if (Status != GL_FRAMEBUFFER_COMPLETE) 
{
    return false;
}
```

위 함수에서 에러를 뿜어내길래 확인해보니,
`glTexImage2D` Function을 호출했을 때 Depth Texture Format을 잘못 설정하면서 발생한 문제였습니다.
꼭, Texture Spec을 정의할 때, 관련 API의 공식 문서를 보면서 코딩해야 한다는 걸 뼈저리게 느꼈습니다😂.

참고로, Error List와 각각의 의미는 아래와 같습니다.

|리턴값|설명|
|------|---|
|GL_FRAMEBUFFER_COMPLETE|FBO가 정상적으로 바인딩 됨. 렌더링 가능|
|GL_FRAMEBUFFER_UNDEFINED|FBO Bind는 0, 기본 프레임 버퍼가 존재하지 않음|
|GL_FRAMEBUFFER_INCOMPLETE_ATTACHMENT|렌더링을 위해 활성화된 버퍼 중 하나가 불안정하다.|
|GL_FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT|FBO에 Buffer가 Attach 되지 않음.|
|GL_FRAMEBUFFER_UNSUPPORTED|내부 버퍼 조합이 지원되지 않음.|
|GL_FRAMEBUFFER_INCOMPLETE_LAYER_TARGETS|Frame Buffer가 레이어텍스쳐가 아니거나, 동일한 타겟에 바인딩 되지 않음.|

<br>

`glCheckFramebufferStatus` 결과가 문제 없다면 Texture와 Frame Buffer 둘 다 문제가 없다는 거니,
`glBlitFramebuffer` Function을 호출하여 main Buffer에 있는 내용을 fbo에 복사해 줍니다.
복사를 할 Buffer를 `GL_READ_FRAMEBUFFER` 로, 복사한 내용을 붙일 Buffer를 `GL_DRAW_FRAMEBUFFER` 로 bind하여 Blit해줍니다.

```cpp
// Main Buffer를 Read Buffer로, FBO를 Draw Buffer로 지정하여 Main Buffer에 있는 정보를 FBO로 복사해 준다.
glBindFramebuffer(GL_READ_FRAMEBUFFER, 0);  
glBindFramebuffer(GL_DRAW_FRAMEBUFFER, fbo);  
glBlitFramebuffer(0, 0, width, height, 0, 0, width, height,  GL_COLOR_BUFFER_BIT, GL_NEAREST); 
glBindFramebuffer(GL_READ_FRAMEBUFFER, 0);       
glBindFramebuffer(GL_DRAW_FRAMEBUFFER, 0);
```

이 결과로 생성된 Texture는 실제 렌더링 시 bind할 수 있는 texture가 됩니다.
저는 여기서 blit을 했지만, Blit을 수행한 시점에 실제 Drawing을 하게 되면, Drawing한 결과물을 실제 Main Buffer에 그릴 Texture로 사용할 수 있게 됩니다.

## 생성된 Texture의 Depth Buffer 사용 방법
Color Texture는 `glTexImage2D` 함수에서 선언한 대로 Color값을 사용할 수 있으며, Depth Texture의 경우, Depth Color 값을 .r로 뽑아내면 확인할 수 있습니다.
예제 코드는 아래와 같습니다.

```cpp
#version 330 core
out vec4 FragColor;
  
in vec2 TexCoords;

uniform sampler2D depthMap;
uniform sampler2D colorMap;

void main()
{             
    float depthValue = texture(depthMap, TexCoords).r;
    vec3 colorValue = texture(depthMap, TexCoords).rgb;
}  
```