---
title: "데이터 통신 - 오류 제어와 흐름 제어"
date: 2019-06-04 10:45:00 -0800
categories: Data Communication Data_Communication
---

# 개요
통신상에서 송신측에서 수신측으로 데이터를 보낼 때 이에 대한 오류를 검출하고 컨트롤할 수 있어야하며, 오버플로우(언더플로우)등의 오류를 방지해야한다.

# 오류제어
### 오류 검출
송신측에서 보내고자 하는 원래의 정보 이외에 별도로 잉여분의 데이터를 추가하는 방식으로, 수신측에서는 이 잉여(Redundancy) 데이터를 검사함으로써 오류 검출이 가능하다. 종류로는 패리티 검사, 블록 합 검사, CRC(Cyclic Redundancy Check), Checksum 등이 있다.

### 패리티 검사
한 블록의 데이터 끝에 한 비트를 추가하는 방식으로 구현이 간단하여 널리 사용된다. 종류로는 1의 전체 갯수가 짝수개가 되게 하는 짝수 패리티, 그리고 1의 전체 갯수가 홀수개가 되게 만드는 홀수 패리티가 있다.