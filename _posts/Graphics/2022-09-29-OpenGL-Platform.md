---
layout: single
title:  "EGL"
category: Graphics
tags: [OpenGL, Graphics]
date: 2022-09-29
comments : true
---

업무를 하다 보면, Rendering API의 플랫폼 별 적용과 관련된 내용을 접하게 됩니다. 최근에 관련 업무를 진행 한 김에 플랫폼 관련한 내용을 알아두는게 좋을 것 같아 공부를 하고 정리를 해 보았습니다.

먼저 OpenGL이 무엇인가에 대해 먼저 살펴 보겠습니다.

## OpenGL
* 3D 그래픽을 화면에 표현하기 위해 필요한 **기본적인 기능들을 모은 Open API** (점을 찍는다던지, 선을 그린다던지)
* **Standard만 있고, Spec만 있다.**
    * 실제 스펙(함수)에 맞게 동작하도록 각 벤더에서 기능을 각자 개발한다.

<br>

## Rendering API와 플랫폼 시스템 사이의 인터페이스
실제 OpenGL을 통해 운영체제 별로 그림을 그리기 위해선 Context나 Surface가 필요합니다.
하지만 OpenGL은 그저 스펙이기 때문에 직접 플랫폼 내에서 Context나 Surface를 만드는 일은 할 수 없습니다. 

Platform API는 OpenGL을 사용하여 각 플랫폼에 맞는 방식으로 그릴 수 있게 만드는 일종의 인터페이스 역할을 하는 API입니다.
각 운영체제 별로 사용하는 API가 다르며 (WGL(Windows), Cocoa(Mac)), 이런 각각 다른 인터페이스를 신경 쓰지 않도록 만들어주는 GLUT(옛날거라서 잘 안씀), GLFW 등의 Lib도 있습니다.

Platform API 중 EGL을 기준으로 설명해 보려 합니다.

<br>

## EGL
* Khronous에서 만든 Rendering API (E.g - OpenGL) 과 플랫폼(Windows, Android..) 간의 인터페이스 역할을 하는 API 입니다.
* **특정한 플랫폼**에서 Rendering API를 사용할 수 있도록 연결 시키는 용도로 사용하고 있습니다.
    * Windows에서 EGL을 이용하면 Windows의 윈도우에 렌더링을 수행할 수 있다고 생각하면 좀 더 편하게 생각할 수 있을 것 같습니다.
* WGL(Windows), Cocoa(Mac) 등과는 달리 EGL은 플랫폼 독립적입니다.

<br>

## Surface와 Context
실제 모니터에 그리기 위해선 Surface와 Context가 필요합니다. 이걸 설명하기 위해선 OpenGL의 동작을 먼저 알아야 합니다.

### OpenGL의 기본 동작
* 셋팅 : 기본 동작 중 셋팅 값을 저장
    * E.g) 그릴 선의 색은 빨간색이다.
* 그리기 : 실제 Drawing을 수행
    * E.g) 선을 그린다


이 중 Context는 셋팅을 위한 개념이며, 그리는 버퍼가 Surface에 대한 개념입니다.
자세한 내용은 아래와 같습니다.


### Context 
셋팅 동작을 수행하기 위한 공간입니다. Context에 명령을 쌓아두면, GPU가 임의로 명령들을 가져가서 수행합니다.


### Surface
그려지거나 화면상에 표시되는 일종의 이미지 버퍼를 추상화 한 개념입니다.
Surface는 아래 정보를 포함하고 있습니다.
* Surface Color Buffer 정보
* Depth Buffer, Stencil Buffer 등의 유무
* 그외 다른 여러 가지 정보

Surface는 운영체제 마다 개수는 다르지만 여러 개의 Buffer가 지원 됩니다.
만약 단순한 이미지 버퍼일 경우, 그려지는 도중 실제로 표시될 수 있는 문제가 있기 때문에 (Tearing), 보통은 Back Buffer에서 그리기가 완료된 Buffer를 표시하고 있는 Buffer랑 바꿔치기 하는 방식으로 frame이 진행됩니다.

## EGL에서의 Surface / Context 관리
실제 EGL을 활용한 Surfae / Context 관리 방법은 아래와 같습니다.

<br>

### EGL 기본 설정
EGL은 스스로 Display를 생성할 수 없습니다. 그래서 각 플랫폼 별로 디스플레이를 생성할 수 있는 함수를 호출해서 Display의 Handle을 받아야 합니다.
Windows OS를 기준으로 했을 때, 아래 함수를 호출해서 운영체제를 통해 Display Handle을 받을 수 있습니다.
```cpp
HWND wnd = CreateWindowEx( WS_EX_OVERLAPPEDWINDOW, szWindowClass, surfaceName, WS_SYSMENU | WS_MINIMIZEBOX | WS_VISIBLE , 
		CW_USEDEFAULT, 0, width, height, NULL, NULL, hInst, NULL);
```

Window Handle을 통해 Window 창의 위치, 즉 우리가 그린 그림을 표시해야 할 위치를 받습니다.
```cpp
EGLDisplay eglDisplay = eglGetDisplay( (NativeDisplayType) GetDC( wnd ) );
```

EGL이 표시해야 할 Display를 알게 되었다면, EGL을 초기화 해 주어야 합니다.
```cpp
eglInitialize( m_eglDisplay, &major, &minor )
```

EGL 초기화가 성공적으로 완료 되었다면, EGL이 사용할 Rendering API를 지정해주어야 합니다.
```cpp
// EGL_OPENVG_API
// EGL_OPENGL_API
// EGL_OPENGL_ES_API 
eglBindAPI(EGL_OPENGL_API)
```

### EGL에서의 Surface 관리
위에서 말했듯이, Surface는 여러 정보를 가지고 있습니다.
eglChooseConfig() 함수로 Surface의 설정 값을 전달해 주어야 합니다. 
Config 설정이 성공적으로 완료된다면, 설정된 화면 구성으로 Surface를 생성해 줍니다.
```cpp
EGLConfig		eglConfig;
eglChooseConfig( eglDisplay, attrib_list, &eglConfig, 1, &configs );
EGLSurface m_eglSurface = eglCreateWindowSurface( eglDisplay, eglConfig, m_eglWindow, attrib_list );
```


### EGL에서의 Context 생성
eglCreateContext를 호출하면 Rendering Context를 생성할 수 있습니다.
여러 Context 사이에 Texture, Shader와 같은 객체 공유를 위해 세 번째 파라미터가 셋팅 될 수 있으며, 따로 Context간 Texture, Shader 등을 공유할 필요가 없다면 EGL_NO_CONTEXT를 파라미터로 넣어주면 됩니다.
```cpp
EGLint context[] = { EGL_CONTEXT_CLIENT_VERSION, 3, EGL_NONE };
m_eglContext = eglCreateContext( eglDisplay, eglConfig, EGL_NO_CONTEXT를, context );
```

context 삭제를 위해선 eglDesctoyContext()를 반드시 호출해야 합니다.


### Surface - Context
EGL Surface와 EGL Context 연결을 위해 eglMakeCurrent() 함수를 호출합니다.
```cpp
eglMakeCurrent( eglDisplay, eglSurface, eglSurface, eglContext );
```
Surface 설정이 잘못되었거나 Context가 호환되지 않을 경우, EGL_NO_CONTEXT Error가 반환됩니다.


### eglSwapBuffers
Rendering Surface의 결과를 Window Surface와 교체하는 Function 입니다. 해당 함수 호출 직후 모든 GL 명령들을 GPU로 전달합니다.
```cpp
eglSwapBuffers( eglDisplay, eglSurface );
```

