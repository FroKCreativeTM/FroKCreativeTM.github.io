---
title: "시스템 프로그래밍 1주차 - OT"
date: 2019-03-08 21:00:28 -0800
categories: linux systemProgramming
---

### 이 글에서 다룰 내용
간단하게 리눅스에 대해 알아보고, 시스템 프로그래밍과 Shell script, system call에 대해 알아본다.

# 리눅스?

리눅스는 기본적으로 모든 것이 공개된 오픈 소스이다. 그리고 서버에 많이 사용되던 OS인 Unix와 유사하게 생겼고 호환되며, 오히려 더 강력한 면도
있기에, 그 결과 현재는 서버 시장의 약 70%를 차지하고 있다.
[출처 : https://www.zdnet.com/article/linux-foundation-finds-enterprise-linux-growing-at-windows-expense/]
또한 리눅스는 슈퍼 컴퓨터 시장에서 Unix를 거의 100% 대체하였으며 Android, Tizen, iOS 등의 휴대폰의 OS의 기반이 되었다.

또한 Windows OS는 GUI 기반이라 자동화가 어렵다란 단점이 있다. 그런 단점을 Linux의 강력한 Shell Script 언어로 해결이 가능하다.

# 시스템 프로그래밍

C언어의 라이브러리 함수(ex. printf, scanf, putc... etc) 대신 system call(ex. write, read, mkdir ... etc)의 사용을 해서 더 작고 빠른 수행이 가능하며
더욱 더 유연한 프로그램을 개발하는 것이 가능하다. 단 system call은 OS마다 다 다르며 이 call들을 사용하기 위해서는 해당 OS를 잘 알아야 한다.
(예로 들어 Windows 환경은 WinApi나 MFC 등을 알아야 활용이 가능하다.)


# 리눅스 Shell
## Shell이란?

OS Service를 쉽게 접근하기 위한 UI, 현대의 모든 OS는 GUI(Graphic User Interface)와 Text shell을 동시에 제공한다.
예로 들어 Windows OS또한 'cmd'라고 불리는 명령 프롬프트를 이용해 Text shell을 지원한다. 리눅스 또한 여러 종류의 text shell과 graphic shell을 지원하며,
원하는대로 수시로 바꿔가며 사용할 수 있다. text shell의 대표적인 예로 bash, sh, csh, zsh ... etc가 있다.

# 원격 접속

Linux는 근본적으로 다중 사용자 시스템이다. 그래서 원격 접속이 필요한데, 이를 위한 protocol로 ssh, sftp, vnc... etc가 있다.
또한 이를 지원해주는 프로그램으로 SSH Windows client나 Xshell이 있다.
[Xshell 무료 라이센스 - https://goo.gl/T6a2Vt]

# 리눅스 시스템 프로그래밍

Linux system call을 사용해서 프로그래밍한다. (이는 후에 설명) 이 system call을 이용해 우리는 system software를 조종한다. 
여기서 잠시, system software란 무엇일까? system software란 하드웨어를 작동 또는 컨트롤하고 응용 소프트웨어의 수행을 위한 platform을 제공하기 위한
하드웨어와 응용 소프트웨어 사이에 있는 소프트웨어이다. 이 소프트웨어를 구성하는 요소로는 OS(운영체제), 프로세스관리, 입출력관리, 기억장치관리, 파일시스템관리가 있다.

# 라이브러리 함수와 system call의 차이

라이브러리 함수는 기본적으로 수행 모드가 user 모드이다. 또한 예로 들어 간단히 c로 짠 코드들이 OS와 하드웨어에 무관하게 돌아가며, 효율은 저효율이지만 기능들이 라이브러리 함수 기능에 한정된다.
단 프로그래밍이 쉽다란 장점이 있다. 하지만 system call은 kernel 차원에서 돌리는 것이기에 OS와 hardware에 굉장히 직접적으로 연관이 있으며 프로그래밍은 어렵다.
하지만 효율이 높으며, 유연한 control이 가능하다는 장점이 있다. 예로 들어 mkdir, kill 등은 shell과 공유를 하기에 코드 차원에서 유연하다.

# 작업을 수행하는 여러 가지 방법

작업을 수행하는데에는 여러 가지 방법이 있는데 이를 가르는 기준은 interactive적이냐 아니냐에 있다.
interactive적인 작업이란 text shell에 직접 명령어를 입력하거나 GUI에 mouse, keyboard로 수행하는 등 수행 도중에 직접 관여하여, 수행을 조정하는 작업을 의미한다.
반대로 non-interactive적인 작업이란 shell의 명령으로 구성된 프로그램을 작성(이를 shell script라 한다.)를 작성하여 이를 실행하거나 
C 프로그램을 만들어 compile하여 실행파일을 만들고 이를 실행하는 방식으로 자동화가 가능하다는 장점이 있다.
