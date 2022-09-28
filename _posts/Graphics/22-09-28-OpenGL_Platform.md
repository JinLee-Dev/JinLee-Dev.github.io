---
layout: single
title:  "OpenGL과 플랫폼"
category: OpenGL
tag: OpenGL Graphics
permalink: "/CMake/"
categories:
  - OpenGL
date: 2022-09-29
comments : true
---

업무를 하다 보면, Rendering API의 플랫폼과 관련된 내용을 간간히 접하게 됩니다. 최근에 이와 같은 내용을 다시 한 번 접하게 됐는데, 이번 기회에 플랫폼 관련한 내용을 알아두는게 좋을 것 같아 공부를 하고, 정리를 해 보았습니다.

먼저 관련된 내용을 접하기 전에, OpenGL이 무엇인가에 대해 먼저 살펴 보겠습니다.

## OpenGL
----
* 3D 그래픽을 화면에 표현하기 위해 필요한 **기본적인 기능들을 모은 Open API** (점을 찍는다던지, 선을 그린다던지)
* low-level API 
    * 그림을 그리는데 가장 기본적인 기능만 있기 때문.
* **Standard만 있고, Spec만 있다.**
    * 실제 스펙(함수)에 맞게 동작하도록 각 벤더에서 기능을 각자 개발한다.
<br>
<br>

## Rendering API와 플랫폼 시스템 사이의 인터페이스
----
OpenGL은 위에서 말했다시피 사양만 정의되어 있습니다. 구체적으로 어떠한 플랫폼에서 어떤 방식으로 그리는지에 대한 정의가 되어 있지 않습니다. 실제 화면에 그리기 위해선, 특정한 플랫폼에서 OpenGL을 통해 물체를 그리기 위해 인터페이스 역할을 하는 API가 있어야 합니다. 여러 종류 (WGL, Cocoa, EGL)가 있지만, 저는 **EGL**을 기준으로 설명해 보려고 합니다.


## EGL
-----
* Khronous에서 만든 Rendering API (E.g - OpenGL) 과 플랫폼(Windows, Android..) 간의 인터페이스 역할을 하는 API 입니다.
* **특정한 플랫폼**에서 Rendering API를 사용할 수 있도록 연결 시키는 용도로 사용하고 있습니다.
    * Windows에서 EGL을 이용하면 Windows의 윈도우에 렌더링을 수행할 수 있다고 생각하면 좀 더 편하게 생각할 수 있을 것 같습니다.
* WGL(Windows), Cocoa(Mac) 등과는 달리 EGL은 플랫폼 독립적입니다.

<br>

## Surface와 Context
-----
실제 모니터에 그리기 위해선 Surface와 Context가 필요합니다. 이걸 설명하기 위해선 OpenGL의 동작을 먼저 알아야 합니다.

<br>

### OpenGL의 기본 동작
* 셋팅 : 기본 동작 중 셋팅 값을 저장
    * E.g) 그릴 선의 색은 빨간색이다.
* 그리기 : 실제 Drawing을 수행
    * E.g) 선을 그린다

<br>

이 중 Context는 셋팅을 위한 개념이며, 그리는 버퍼가 Surface에 대한 개념입니다.
자세한 내용은 아래와 같습니다.

<br>

### Context 
* 셋팅 동작을 수행하기 위한 공간입니다.
* Context에 명령을 쌓아두면, GPU가 임의로 명령들을 가져가서 수행합니다.

<br>

### Surface
* 그려지거나, 표시되는 버퍼를 지칭합니다.
* 실제 렌더링 되는 대상이라고 볼 수 있는 이미지 버퍼를 추상화한 것이라고 보면 됩니다.
* Surface에는 아래와 같은 정보들이 포함되어 있습니다.
    * Surface Color Buffer 정보
    * 각 Color에 몇 비트나 사용할지?
    * Depth Buffer, Stencil Buffer 등의 유무

<br>
<br>

실제 EGL을 활용한 Surfae / Context 관리 방법은 아래와 같습니다.

## EGL에서의 Surface 관리
---
* eglChooseConfig() 함수로 eglSurface가 가질 화면 구성을 설정한다.
* eglCreateWindowSurface() 함수로 설정된 화면 구성으로 Surface를 생성한다.
* 렌더링이 끝난 후에는 eglDestroySurface()를 호출해준다.

<br>

## EGL에서의 Context 관리
---
* eglCreateContext()로 Rendering Context를 설정할 수 있다.
* shared_context 인자는 Context 사이에 텍스쳐와 쉐이더 같은 객체의 공유를 위해서 사용된다.
* context 삭제를 위해선 eglDesctoyContext()를 반드시 호출해야 한다.

<br>

## Surface - Context
---
* 가장 먼저 사용하고자 하는 Context를 알리기 위해 eglMakeCurrent() 함수를 호출해준다.
* Surface 설정이 잘못되었거나 Context가 호환되지 않을 경우, EGL_NO_CONTEXT Error가 반환된다.

<br>

## eglSwapBuffers
---
* Rendering Surface의 결과를 Window Surface와 교체하는 Function
* 해당 함수 호출 직후 모든 GL 명령들을 GPU로 전달한다.
