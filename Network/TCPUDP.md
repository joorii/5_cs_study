# TCP/UDP



 ## TCP(Transmission Control Protocol)

- 두 호스트가 교환하는 데이터와 승인 메세지의 형식을 정의하여, 서버와 클라이언트간의 데이터를 신뢰성있게 전달하기 위해 만들어진 규약
- **연결지향** : 가상의 연결통로를 설정해서 통신하는 방식(가상회선)
- 스트림 기반의 전송방식, 정해진 크기가 아닌 임의의 크기로 나누어 연속해서 전송하는 방식이므로 반드시 순서번호가 필요!!!
- **신뢰할 수 있는 프로토콜** : 흐름제어(상대방이 받을 수 있는 정도만 데이터를 전송), 오류제어(데이터의 오류나 누락 없이 안전한 전송을 보장. 혼잡제어(네트워크 혼잡정도에 따라 송신자가 데이터 전송량을 제어))
- MSS - TCP 데이터 부의 최대 바이트 수, 연결 설정 과정에서 MSS 정보 설정 가능/ MTU에 영향 받으며, MTU-IP헤더-TCP헤더가 MSS

[![tcp header](https://evan-moon.github.io/static/ac69210c44cd473bcb737665d590b124/6af66/tcp-header.png)](https://evan-moon.github.io/static/ac69210c44cd473bcb737665d590b124/c7bb6/tcp-header.png)

- 헤더 구조(TCP 헤더는 가변 길이며, 20byte  - 60byte)

  1. source port(16 bit) - 송신 포트 번호
  2. destination port(16 bit) - 수신 포트 번호
  3. sequence number(32 bit) - 송신 데이터 순서 번호, 송신 시 전송되는 데이터의 시작 순서 번호(ISN)
  4. acknowledagement number(32 bit): 상대방이 다음에 전송할 순서 번호. ACK와 함께 상대방이 다음에 전송할 순서 번호를 담아서 보냄
  5. HLEN(4 bit) - 헤더의 길이(tcp 헤더가 가변 길이이므로 필요)
  6. Reserved(4 bit) - 사용안하는 비트라 넘어가도 됨
  7. **Control flag**(6 bit) - 가장 중요한 필드
     - URG - 긴급 데이터 설정
     - ACK - 수신 확인 응답
     - PSH - 송수신 버퍼에 있는 데이터를 즉시 처리(전송 계층의 정보를 응용 계층으로 바로 올림)
     - RST - 강제 연결 종료
     - SYN - 연결 설정
     - FIN - 정상 연결 종료
  8. window size(16 bit) - 수신 측에서 송신 측에 보내는 수신 버퍼의 여유 공간 -> 흐름제어에 관련된 필드
  9. checksum(16 bit) - 헤더를 포함한 전체 세그먼트에 대한 오류를 검사 -> 오류제어에 관련된 필드
  10. Urgent pointer(16 bit) - URG 필드가 활성화되어 있을시, 긴급 데이터의 주소값

   

## TCP 3 way handshake & 4 way handshake

> 연결을 성립하고 해제하는 과정을 말한다



### 3 way handshake 

 통신하기에 앞서, 논리적인 경로를 성립하기 위해 **3 way handshake 과정**

[![img](https://camo.githubusercontent.com/4acea6af95884347810f057d00c6c4643a56d4a7dbbdf49740745560cd45cc1f/68747470733a2f2f6d656469612e6765656b73666f726765656b732e6f72672f77702d636f6e74656e742f75706c6f6164732f5443502d636f6e6e656374696f6e2d312e706e67)](https://camo.githubusercontent.com/4acea6af95884347810f057d00c6c4643a56d4a7dbbdf49740745560cd45cc1f/68747470733a2f2f6d656469612e6765656b73666f726765656b732e6f72672f77702d636f6e74656e742f75706c6f6164732f5443502d636f6e6e656374696f6e2d312e706e67)

1. 클라이언트가 서버에게 SYN 패킷을 보냄 (sequence : x, 이때 x는 0이 아닌 랜덤 값)
2. 서버가 SYN(x)을 받고, 클라이언트로 받았다는 신호인 SYN에 대한 ACK와 SYN 패킷을 보냄 (sequence : y, ACK : x + 1/ 이때 ACK의 acknowledege number는 x+1)
3. 클라이언트는 서버의 응답은 ACK(x+1)와 SYN(y) 패킷을 받고, ACK(seq- x+1, ack-y+1)를 서버로 보내고, TCP의 소켓을 ESTABLISHED로 설정



### 4 way handshake - 연결 해제

연결 성립 후, 모든 통신이 끝났다면 해제(정상 연결 해제)

[![img](https://camo.githubusercontent.com/8bb8960e46a3bfada6a237a7a91bce75a0a3e0e34eab5c1f5143ca6fe34d0b5f/68747470733a2f2f6d656469612e6765656b73666f726765656b732e6f72672f77702d636f6e74656e742f75706c6f6164732f434e2e706e67)](https://camo.githubusercontent.com/8bb8960e46a3bfada6a237a7a91bce75a0a3e0e34eab5c1f5143ca6fe34d0b5f/68747470733a2f2f6d656469612e6765656b73666f726765656b732e6f72672f77702d636f6e74656e742f75706c6f6164732f434e2e706e67)

1. 클라이언트는 서버에게 연결을 종료한다는 (FIN+ACK)을 보냄(FIN_WAIT_1 - 첫번째 FIN+ACK 패킷에 대해 ACK를 대기하는 상태)
2. 서버는 연결 종료에 대한 ACK를 클라이언트에게 보낸다. (CLOSE_WAIT - 첫 번째 FIN+ACK 수신 후 와 두번째 FIN+ACK 전송할때까지의 대기 상태)
3. 서버는 응용 계층의 tcp 연결 종료를 모두 할 때까지 대기 후 클라이언트에게 FIN+ACK를 보냄
4. 클라이언트는 FIN을 받고, 확인했다는 ACK를 서버에게 보냄. (아직 서버로부터 받지 못한 데이터가 있을 수 있으므로 TIME_WAIT(마지막 ACK를 보낸 후 잠시 대기하는 상태)을 통해 기다린다.)

- 서버는 ACK를 받은 즉시 소켓을 닫는다 (Closed)
- TIME_WAIT 시간이 끝나면 클라이언트도 닫는다 (Closed)



## TCP (흐름제어/혼잡제어)

- 연결 지향, 신뢰 지향 프로토콜의 문제점

  - 손실 : 전송 과정 중 packet이 손실될 수 있음 
  - 순서 바뀜 : MTU 범위를 초과한 packet은 분할해서 보내야 하는데 순서가 바뀔 수 있음
  - Congestion : 네트워크가 혼잡시 분할한 패킷 중 일부가 도달하지 않을 수 있음
  - Overload : 수신자가 받아들일 수 없는 용량의 데이터가 수신자의 버퍼에 쌓일 수 있음

- 흐름제어/혼잡제어
  - 흐름제어 
    - 송신측과 수신측의 데이터 처리 속도 차이를 해결하기 위한 기법
    - Flow Control은 receiver가 packet을 지나치게 많이 받지 않도록 조절
    - 수신자가 송신자에게 자신의 수신 버퍼의 상태를 알려주는 것이 핵심
  - 혼잡제어 : 송신측의 데이터 전달과 네트워크의 데이터 처리 속도 차이를 해결하기 위한 기법
  
  

### 1. 흐름제어 (Flow Control)

- 수신측이 송신측보다 데이터 처리 속도가 빠르면 문제없지만, 송신측의 속도가 빠를 경우 문제가 생긴다.

- 수신측에서 제한된 저장 용량을 초과한 이후에 도착하는 데이터는 손실 될 수 있음

- 이러한 위험을 줄이기 위해 송신 측의 데이터 전송량을 수신측에 따라 조절해야한다.

- 해결방법

  - Stop and Wait : 매번 전송한 패킷에 대해 확인 응답을 받아야만 그 다음 패킷을 전송하는 방법

  - 매번 확인하는 것을 의미(버퍼 괜찮음? 버퍼 괜찮!)

    [![img](https://camo.githubusercontent.com/cb7f08015fa52f106d69a4cab2c4ad48129e2b71133e368808c51578c01f5437/68747470733a2f2f74312e6461756d63646e2e6e65742f6366696c652f746973746f72792f323633423744344535373135454345423332)](https://camo.githubusercontent.com/cb7f08015fa52f106d69a4cab2c4ad48129e2b71133e368808c51578c01f5437/68747470733a2f2f74312e6461756d63646e2e6e65742f6366696c652f746973746f72792f323633423744344535373135454345423332)

  - Sliding Window 

    - 수신측에서 설정한 윈도우 크기만큼 송신측에서 확인응답없이 세그먼트를 전송할 수 있게 하여 데이터 흐름을 동적으로 조절하는 제어기법

    - 다. 슬라이딩 윈도우의 구성

      | ![img](http://www.skby.net/blog/wp-content/uploads/2018/11/1-86.png) |                                                              |
      | ------------------------------------------------------------ | ------------------------------------------------------------ |
      | 윈도우 크기                                                  | – 전송했으나 확인 응답 받지 못한 데이터와 지연없이 전송 가능 데이터 합계 |
      | 송신버퍼 크기                                                | – 수신 측의 여유 버퍼 공간을 반영하여 동적으로 변경          |
      
      ##  
      
      ## II. 슬라이딩 윈도우 알고리즘 설명
      
      ![img](http://www.skby.net/blog/wp-content/uploads/2018/11/2-47.png) 
      
      |      구분       |                             설명                             |
      | :-------------: | :----------------------------------------------------------: |
      | 윈도우 열림동작 | 수신측 ACK 도착, 윈도우 우측 경계 오른쪽 이동 늘어난만큼 더 많은 데이터의 전송 가능 |
      | 윈도우 닫힘동작 | 데이터 전송, 윈도우 좌측 경계 오른쪽 이동 전송측은 이 데이터에 대해 관여할 필요 없음 |
      | 윈도우 크기결정 | 수신 측 윈도우와 혼잡 윈도우 크기 중 작은 값 ACK 포함 세그먼트를 사용하여 상대방에게 알림 혼잡상태가 발생 않도록 네트워크에서 결정 값 |



### 2. 혼잡제어 (Congestion Control)

- 송신측의 데이터는 대형 네트워크를 통해 전달된다. 만약 한 라우터에 데이터가 몰릴 경우, 자신에게 온 데이터를 모두 처리할 수 없게 된다. 이런 경우 호스트들은 또 다시 재전송을 하게되고 오버플로우나 데이터 손실을 발생
- 네트워크의 혼잡을 피하기 위해 **송신측**에서 보내는 데이터의 전송속도를 강제로 줄이는 것을 의미
- 혼잡 - 네트워크 내에 패킷의 수가 과도하게 증가하는 현상을 혼
- 해결 방법
  - [![img](https://camo.githubusercontent.com/e5daaf381dd565e77a2827a78e339a708cb239e302354ff75b446f39e0134286/68747470733a2f2f74312e6461756d63646e2e6e65742f6366696c652f746973746f72792f323536453339343235373135463130313033)](https://camo.githubusercontent.com/e5daaf381dd565e77a2827a78e339a708cb239e302354ff75b446f39e0134286/68747470733a2f2f74312e6461756d63646e2e6e65742f6366696c652f746973746f72792f323536453339343235373135463130313033)
  - AIMD(Additive Increase / Multiplicative Decrease)
    - 처음에 패킷을 하나씩 보내고 이것이 문제없이 도착하면 window 크기(단위 시간 내에 보내는 패킷의 수)를 1씩 증가시켜가며 전송
    - 패킷 전송에 실패하거나 일정 시간을 넘으면 패킷의 보내는 속도를 절반으로 줄인다.
    - 문제점은 나중에 네트워크에 진입하는 호스트가 불리하며, 초기에 네트워크의 높은 대역폭을 사용하지 못함
    - 따라서  네트워크가 혼잡해지는 상황을 늦게 탐지할 수밖에 없고 네트워크가 혼잡해지고 나서야 대역폭을 줄이는 방식
  - Slow Start (느린 시작)
    - Slow Start 방식은 패킷을 하나씩 보내면서 시작하고, 패킷이 문제없이 도착하면 각각의 ACK 패킷마다 window size를 1씩 늘려준다. 즉, 한 주기가 지나면 window size가 2배로 된다.
    - 전송속도는 지수 함수 꼴로 증가한다. 대신에 혼잡 현상이 발생하면 window size를 1로 떨어뜨리게 된다.
    - window size의 절반까지는 지수 함수 꼴로 창 크기를 증가시키고 그 이후부터는 완만하게 1씩 증가.
  - Fast Retransmit (빠른 재전송)
    - 빠른 재전송은 TCP의 혼잡 조절에 추가된 정책.
    - 수신 측에서 먼저 도착해야할 패킷이 도착하지 않고 다음 패킷이 도착한 경우에도 ACK 패킷을 보냄.
    - 이럴 시에, 송신 측에서는 순번이 중복된 ACK 패킷을 받게 된다. 이것을 감지하는 순간 문제가 되는 순번의 패킷을 수신 측에게 재전송함.
    - 중복된 순번의 패킷을 3개 받으면 재전송을 하게 된다. 약간 혼잡한 상황이 일어난 것이므로 혼잡을 감지하고 window size를 줄임.
  - Fast Recovery (빠른 회복)
    - 혼잡한 상태가 되면 window size를 1로 줄이지 않고 반으로 줄이고 선형증가시키는 방법이다. 이 정책까지 적용하면 혼잡 상황을 한번 겪고 나서부터는 순수한 AIMD 방식으로 동작하게 된다.



## UDP(user datagram protocol)

- 비연결형 프로토콜 - 데이터그램 기반 전송방식, 이에 따라 데이티를 정해진 크기로 전송함.(순서번호, acknowledege number 필요 없음)

- 비신뢰성 프로토콜 - 흐름제어, 오류제어, 혼잡제어 없음/ 단 오류 검사는 하므로 checksum 피르드는 존재함

- 단순하고 가벼운 프로토콜이라 전송속도가 빠르며, 한 번의 패킷 송수신으로 완료되는 서비스에 유리(NTP, DNS)

- 헤더 구조 - 8byte로 고정

  ![img](https://t1.daumcdn.net/cfile/tistory/99B12B385BD6DC0F03)

  - source port : 출발지 포트
  - destionation port : 목적지 포트
  - total length port : 헤더와 데이터부를 포함한 전체길이
  - checksum 전체 데이터그램에 대한 오류를 검사하기 위한 필드

  

  ## 참고

  - https://github.com/gyoogle/tech-interview-for-developer
  - http://blog.skby.net/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%94%A9-%EC%9C%88%EB%8F%84%EC%9A%B0sliding-window/
  - 2021 정보보안기사 실기 이론편 1(탑스핏)