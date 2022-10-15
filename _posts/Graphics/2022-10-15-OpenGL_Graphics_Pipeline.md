---
layout: single
title:  "OpenGL Graphics Pipeline"
category: Graphics
tags: [OpenGL, Graphics]
date: 2022-10-15
comments : true
---

    OpenGL로 업무를 진행하면서, 왜 이건 되고, 이건 안될까? 라는 생각을 많이 했었습니다.
예를 들면, 왜 렌더링 되지 않는 Pixel의 깊이 값은 가지고 올 수 없는건가? 라던지 도형끼리 만의 Alpha blending은 왜 안되는가? 같은 생각을 주로 했었던 것 같습니다.

이러한 궁금증은 Pipeline을 공부하면서 확실히 왜 그런가? 에 대핸 이유를 알 수 있었습니다.
지금부터 이 Pipeline 과 관련된 내용을 저 나름대로 정리한 것을 포스팅 하겠습니다.

## Rendering Pipeline?

- 3차원 공간에 존재하는 물체를 2차원 평면 상에 보여주기 위한 과정

![GraphicsPipeline](/assets/img/Graphics_Pipeline.png)

- OpenGL 렌더링 파이프라인 과정
    
![PipelineOrder](/assets/img/Pipeline_order.png)
    

## Vertex Specification

순서가 정해진 Vertex List를 Pipeline으로 전달하는 과정입니다.
이 Vertex들의 순서에 따라 Primitive(OpenGL에서는 Patch라고도 부르며, 삼각형 등등, 한 도형의 단위를 말함) 를 정의할 수 있습니다.
Vertex Data는 일종의 Attribute의 리스트로, 여러 종류의 좌표를 넣을 수 있습니다.

* Position
* Texture
* Normal

### Vertex Rendering
Vertex Data가 정의가 되면 Primitive(가장 작은 도형) 단위로 Vertex Rendering이 진행됩니다.

## Vertex Processing

- Vertex Processing Stage는 거의 Programmable 합니다.

### Vertex Shader
3D 물체를 구성하는 Vertex들의 위치를 Matrix등을 이용하여 화면 상의 좌표(2D)로 변환하는 과정입니다.
* Input : Vertices (X, Y, Z, tX, tY, tZ...)

그리고 Vertex들은 Input - Output이 1:1 맵핑이 되어야 합니다.

* 예시 : Vertex가 3개고, Position, Texture, Normal 좌표가 Attribute라면, 모든 Vertex는  Position, Texture, Normal 좌표가 반드시 들어가 있어야 합니다.

이렇게 데이터가 구성된다면 Vertex 처리를 좀 더 효율적으로 계산할 수 있습니다.

그리고 Index를 넣게 되면, 같은 index 간에는 추가적인 계산을 하지 않게 되어 있기 때문에 Index가 성능에 좋습니다.

호출 빈도는 **Vertex 1개당 1번**. 
Index가 들어가 있는 경우, 해당 Vertex 계산은 중복 수행이 안 되고 한 번만 수행됩니다.

Vertex Shader는 Programmable 한 Pipeline에서 유일하게 필수적인 과정입니다. Fragment Shader가 없어도 Vertex Shader가 있으면 일단 그릴 수는 있습니다. 

다만 Fragment Shader를 안 쓰게 되면 Color 값을 계산하지 않으니, 그린 결과가 직접 Surface 상에는 나오지 않고 Depth Buffer에 데이터가 입력되는 것을 확인하실 수 있습니다. 

### Tessellation
테셀레이션의 사전적 의미는 아래 이미지와 같이 도형들로 겹치지 않으면서 빈틈없게 평면을 채우는 것입니다.
![tessellation](/assets/img/tesellation.png)
    
Graphics Pipeline에서의 테셀레이션도 어떻게 보면 위 그림이랑 상에서의 의미와 유사한 맥락이 있는데,
패치라고 불리는 고차 Primitive를 더 작은 Primitive로 쪼개는 과정을 Tesellation이라고 합니다.
패치를 통해 오브젝트의 모양을 설정하고 테셀레이션을 적용하여 geometric primitive의 개수를 늘려 더 자연스러운 모델을 생성할 수 있습니다.

![Untitled](/assets/img/Tesellation_Vtx.png)

말로 설명하기 보단 그림으로 설명하는게 쉬울 곳 같아 아래 그림으로 대체합니다.
(출처 : https://docs.unity3d.com/kr/530/Manual/SL-SurfaceShaderTessellation.html))

|Before|After|
|------|---|
|![Untitled](/assets/img/Before_t.png)|![Untitled](/assets/img/After_t.png)|

총 2가지의 테셀레이션 쉐이더는 2가지로 구성되어 있으며, 필수적인 쉐이더는 아닙니다.
    - TCS (Tesellation Control Shader)
        - 얼마나 많이 Tesellation을 할지 정하는 Shader
    - TES (Tesellation Evaluation Shader)
        - Tesellation 위치 계산을 실제로 수행하는 Shader

* 예시 1 : Tesellation으로 Curve 적용하기
(출처 : [https://computeranimations.wordpress.com/2015/03/16/rasterization-of-parametric-curves-using-tessellation-shaders-in-glsl/](https://computeranimations.wordpress.com/2015/03/16/rasterization-of-parametric-curves-using-tessellation-shaders-in-glsl/))

![Example]](/assets/img/tesellation_example.png)
       
* TCS
    
    ```glsl
    #version 440 core
    
    layout (vertices = 4) out;
    
    void main()
    {
        gl_TessLevelOuter[0] = 1;
        gl_TessLevelOuter[1] = 16;
    	
    	gl_out[gl_InvocationID].gl_Position = gl_in[gl_InvocationID].gl_Position;
    }
    ```
    
* TES
    
    ```glsl
    #version 440 core
    
    layout (isolines, equal_spacing, ccw) in;
    
    uniform mat4  matModelView;
    uniform mat4  matProjection;
    
    ///////////////////////////////////////////////////
    // function to evaluate  the Bezier curve
    vec3 bezier(float u, vec3 p0, vec3 p1, vec3 p2, vec3 p3)
    {
    	float B0 = (1.-u)*(1.-u)*(1.-u);
    	float B1 = 3.*u*(1.-u)*(1.-u);
    	float B2 = 3.*u*u*(1.-u);
    	float B3 = u*u*u;
    
    	vec3 p = B0*p0 + B1*p1 + B2*p2 + B3*p3;
    	return p;
    } 
    
    void
    main()
    {
        float u = gl_TessCoord.x;
        float v = gl_TessCoord.y;
    
    	vec3 p0 = vec3( gl_in[0].gl_Position );
    	vec3 p1 = vec3( gl_in[1].gl_Position );
    	vec3 p2 = vec3( gl_in[2].gl_Position );
    	vec3 p3 = vec3( gl_in[3].gl_Position );
    	vec4 pos = vec4( bezier( u, p0, p1, p2, p3), 1. );
    	gl_Position = matProjection * matModelView * pos;
    }
    ```
        

### Geometry Processing
테셀레이션 이후에 Primitive 단위로 계산을 수행하는 쉐이더
계산 빈도는 Primitive당 한 번. Primitive 단위가 Line이라면 두 점을, 삼각형이라면 세 점을 받게 됩니다.
Input으로 들어오는 Vertex 양과, Output으로 나오는 Vertex의 양이 달라질 수 있는 것이 특징입니다.
* 예시 : 법선 벡터 시각화
    * 하나의 Triangle을 Input으로 Normal을 이용하여 각 Vertex가 가지고 있는 Normal을 나타낼 수 있는 동작
    
    ```glsl
    #version 330 core
    layout (triangles) in;
    layout (line_strip, max_vertices = 6) out;
    
    in VS_OUT {
        vec3 normal;
    } gs_in[];
    
    const float MAGNITUDE = 0.4;
    
    void GenerateLine(int index)
    {
        gl_Position = gl_in[index].gl_Position;
        EmitVertex();
        gl_Position = gl_in[index].gl_Position + vec4(gs_in[index].normal, 0.0) * MAGNITUDE;
        EmitVertex();
        EndPrimitive();
    }
    
    void main()
    {
        GenerateLine(0); 
        GenerateLine(1); 
        GenerateLine(2);
    }
    ```

* 결과
![Gepmetry_Example](/assets/img/Geometry_example.png)
    

## Vertex Post-Processing

### Transform Feedback
Vertex Processing 결과를 Buffer Object에 넣는 단계입니다.

### Clipping
버텍스들이 버텍스 쉐이더를 떠날 때 그 위치는 클립 공간에 있다고 합니다.
Vertex Processing 결과 위치가 OpenGL 영역 (x, y : -1.0 ~ 1.0 z : 0.0 ~ 1.0) or 특정 뷰포트 영역 내부에 있으면 점을 살리고, 아니면 버립니다.

## Primitive Assembly
이전 Stage들의 Vertex Data의 Output을 모아 연속된 Primitive로 모으는 과정입니다.
* E.g) Triangle Strip으로 지정된 12개의 Vertex가 전달된다면, 10개의 Primitive로 변환 시킨다.
    

### Face culling
Primitive가 어떤 방향을 향하는지 판단하는 과정입니다. User가 원하는 방향을 기준으로 앞면인지, 뒷면인지 정할 수 있습니다.
정하는 방법은 **Vertex의 순서를 기준으로 CCW인지, CW인지**를 기준으로 앞면, 뒷면을 정한다.
```glsl
void glFrontFace(GLenum mode);
```
![Untitled](/assets/img/CWCCW.png)

위 판단을 통해 안보이는 면(뒷면)을 그리지 않을 수 있는 gl Function이 제공됩니다.
    
```cpp
void glCullFace(GLenum mode);
```
* 예시
    * 우리가 정면에 있는 정육면체 큐브를 그린다고 생각했을 때, 앞, 오른쪽, 위의 면은 보이지만, 뒤, 밑, 왼쪽의 큐브는 보이지 않는 면이 됩니다.
    * 이를 glCullFace를 통해 그리지 않도록 설정할 수 있습니다.
    
    ![Untitled](/assets/img/CWCCW.png)
    

## Rasterization

이전 스테이지에서 얻은 primitive를 이용하여 픽셀을 결정하는데 필요한 데이터인 fragment를 생성하는 과정입니다.

출처 : [https://commons.wikimedia.org/wiki/File:Rasterisation-triangle_example.svg](https://commons.wikimedia.org/wiki/File:Rasterisation-triangle_example.svg)

![Untitled](/assets/img/Rasterization.png)

