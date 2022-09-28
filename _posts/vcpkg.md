---
layout: single
title:  "Vcpkg"
category: C++
tag: C++
---

# vcpkg
C, C++ 라이브러리를 설치, 관리해주는 유틸리티입니다.

간단한 명령줄로도 여러 라이브러리 설치가 가능한 라이브러리이며, 오픈소스입니다.

# 설치 방법 (Windows)
저는 윈도우에서 환경 설정을 해주었으므로 윈도우에서 진행 하도록 하겠습니다.
Getting Started에서는 아래와 같은 환경과, 프로그램이 설치되어 있어야 한다고 합니다.
* Windows 7 or newer
* Git
* Visual Studio 2015 Update 3 or greater with the English language pack

# 설치
Vcpkg를 설치할 폴더를 지정합니다.
많은 용량을 차지하는 라이브러리도 있어, 설치 전에 충분한 용량이 있는지 확인하시는 것이 좋습니다.

vcpkg getting started에서는 `C:\src\vcpkg` 나 `C:\dev\vcpkg` 등의 경로를 권하고 있습니다.

우선 github에서 vcpkg를 다운로드 합니다.

    git clone https://github.com/Microsoft/vcpkg.git


클론이 완료되면 아래 스크립트를 실행 시킵니다.

    ./bootstrap-vcpkg.bat


# 라이브러리 설치 방법
Vcpkg 설치가 완료되면, 아래 명령어로 원하는 라이브러리 들을 받을 수 있습니다.

    vcpkg install 라이브러리명

vcpkg를 이용해 QT를 받는다고 한다면, 아래와 같이 명령어를 호출하면 됩니다.

    vcpkg install qt5 

설치한 라이브러리 실행 와중에 일부 라이브러리가 실행이 안되는 문제가 있어 확인해보니, 64 bit 프로그램이 아니라서 생기는 문제였습니다.
default는 32 bit로 라이브러리를 받을 때는 아래와 같이 추가 명령어를 넣어야 합니다.

    vcpkg install [package name]:x64-windows

# 설치 라이브러리 확인 방법

    vcpkg search 라이브러리명

# Vcpkg 적용 방법
아래 명령어를 수행하면 Visual Studio에서 vcpkg를 통해 다운로드 된 라이브러리를 자동으로 인식하여 경로를 추가로 잡아주거나 할 필요가 없습니다.  

    vcpkg integrate install

CMake에서 vcpkg 내부 라이브러리를 사용해야 한다면, CMake File에 아래와 같은 코드 추가가 필요합니다.

    -DCMAKE_TOOLCHAIN_FILE=(vcpkg 설치경로)/vcpkg/scripts/buildsystems/vcpkg.cmake


# Getting Started 링크
* https://github.com/microsoft/vcpkg#quick-start-windows