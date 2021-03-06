---
title: "셰이더란 무엇인가"
date: 2020-01-16 00:15:28 -0800
categories: Computer_Graphics DirectX Shader
---

# 주의 : 이 글은 Pope Kim님이 쓰신 『셰이더 프로그래밍 입문』이란 책을 정리 차 작성했으며, 문제 발생시 바로 내릴 것입니다.

### 셰이더의 역사

셰이더를 말하려면 일단 CG의 역사를 공부해보자.
그래픽스 라이브러리가 없던 시절에는 삼각형을 그리고 이 삼각형에 어떤 색을 칠할 것인가에 대해서는 소프트웨어가 직접 계산을 했기 때문에
느릴 수 밖에 없었다. 하지만 3차원에 대한 연산은 점차 늘어나기에 3차원 가속칩이 나오기 시작한다. 그러나 이것 또한 표준화가 안 된다는
문제점이 발생을 했다. 그래서 모든 하드웨어에서 잘 돌아가는 표준을 만들자 해서 나온 것이 바로 Direct3D, OpenGL이다.

하지만 표준화가 되니 대부분의 게임이 똑같아 보이는 단점이 생겼다.(PS2 당시가 그랬었다.) 
그렇기에 프로그래머와 아티스트들에게 '어느 정도의 표현할 수 있는 자유는 주자' 해서 나온 기술들이 셰이더 프로그래밍 줄여서 셰이더라고 한다.

### 셰이더란 무엇인가?

정말~ 간단하게 말하면 화면에 출력할 픽셀의 위치와 색상을 계산하는 함수이다.
어원을 따라가면 Shade란 동사가 있는데 이는 색의 농담, 색조, 명함 효과를 주다란 뜻을 가지고 있다.
즉 이 단어의 파생어인 만큼 색이랑 명암같은 따위의 개념과 연관이 있음을 알 수 있다.

그렇다면 이 농담, 색조, 명암을 보고 이 3개를 동시에 결과값으로 출력하는가라 생각할 수 있다. 
하지만 이는 아니다. 이를 전부 조합한 RGBA라는 색상값을 사용하며, 이 최종 색상값을 구하기 위해 여러 기법을 사용한다.

이제 구조적으로 어떻게 되어있나 보자. 내가 공부하는 셰이더 프로그래밍 입문이라는 책은 버텍스 셰이더 그리고 픽셀 셰이더만 다룬다.
위에서 이야기한 RGBA는 이 둘 중 픽셀 셰이더에 적용되는 개념이다.

### 버텍스 셰이더

3D 파이프 라인이 존재하는 이유 중 하나는 3차원 공간에 존재하는 물체를 2차원 평면인 컴퓨터 모니터에 보여주기 위해 존재한다.
이 때카메라의 위치, 또는 각 객체의 삼각형의 위치에 따라 최종 화면에 어디에 나오는가에 대해 처리할 함수가 필요한데,
이를 버텍스 셰이더(정점 셰이더)라고 한다.

일단 CPU와 GPU간 어떻게 통신을 하는 지 알아야한다.
첫번째로 CPU가 정점 데이터(Vertices라 부르는 정점들의 집합) 등, 3D 모델 자체에 대한 정보(폴리곤이 업계 표준)를 GPU로 보내면 이를 받아서
화면 좌표조 변환을 한다. 즉 우리가 그림을 그릴 때 어떤 물체를 보고 이를 캔버스로 옮겨 그리는 것과 똑같다고 보면 된다.
이렇게 물체의 위치를 다른 공가으로 옮기는 과정을 공간 변환(Space transformation)이라고 한다.

결국 모든 정점을 하나씩 공간변환을 하면 3D 물체 자체를 공간변환하는 것과 동일한 결과를 얻을 수 있다.
이것이 바로 버텍스 셰이더가 하는 일이다.

### 픽셀 셰이더

연산 수는 3D 물체를 구성하는 정점의 수만큼 실행이 된다.
근데 아까 3D 물체는 삼각형으로 이루어져 있다고 했다. 그리고 화면을 구성하는 최소 단위는 픽셀이다. (우리가 흔히 FHD도 1920 1080을 곱한 만큼의 픽셀이 있다는 소리다.) 

그럼 이 삼각형들 안에 과연 몇 개의 픽셀(점)들이 들어갈까 궁금하지 않은가?
그럼 화면에 뭔가 그림을 그리려면 픽셀이 어디에 총 몇 개가 그려져야 하는지를 알아야 한다.

이러한 과정을 수행하는 것을 바로 래스터라이저(Rasterizer)란 장치가 수행한다. 이것은 정점 셰이더가 출력하는 정점의 위치를 차례대로
3개씩 모아 삼각형을 만든 뒤 그 안에 들어갈 픽셸들을 찾아낸다. 그러면 여기서 픽셀 셰이더가 나온다.
이 픽셀 셰이더라는 함수는 몇 번 실행이 될까? 바로 래스터라이저가 찾아내는 픽셀 수 만큼 호출된다.

즉 픽셀 셰이더의 주된 임무는 화면에 출력할 최종 색상을 계산하는 것이다.

### 다시 셰이더로
셰이더란 3D 물체를 화면에 그릴 때 그 물체를 구성하는 픽셀들의 위치와 색을 프로그래머가 맘대로 조작하는 것이라고 쉽게 이해가 될 것이다.
그럼 이를 개발하는 것은 무엇일까. 위에서 말한 셰이더의 구조 중 래스터라이저란 장치를 수행하는 그 과정 빼고 정점 셰이더와 픽셀 셰이더에 사용할 함수를
하나씩 만드는 것을 셰이더 프로그래밍이라고 한다.
셰이더를 개발하는 방법은 다양하다. 난 이 중 DirectX와 HLSL(High Level Shader Language)로 실습할 예정이다.
