DNS를 통해 도메인으로 IP 주소까지 얻어내는 방법을 알아봤다. 

이번에는 데이터의 송수신 동작 과정에 대해 알아보자. 

# 데이터 송수신 동작의 개요

소켓 라이브러리가 IP 주소를 조회했던 것처럼 데이터의 송수신에도 소켓 라이브러리가 이용된다. 

![image](https://github.com/user-attachments/assets/86b8b5d9-e6ef-4165-8d88-1be949964605)


소켓이 뭐 별건 아니고 데이터의 통로, 파이프의 끝, 데이터의 출입구 정도로 생각하면 된다. 

서버와 클라이언트가 각각 소켓을 만들고 이를 연결해 데이터를 통신하는 것이다. 

> 💡 **데이터 송수신 과정** 
> 1. 소켓 생성
> 2. 서버가 소켓에 파이프 연결
> 3. 데이터 송수신
> 4. 파이프 분리 및 소켓 말소

이러한 4가지 과정은 OS 내부의 프로토콜 스택에서 일어난다. 

브라우저는 **프로토콜 스택**에 의뢰하여 파이프를 연결하거나 데이터를 전송한다. 

![image](https://github.com/user-attachments/assets/410c4c18-cc7f-40c4-85c4-f9cc9061c7d1)


## 1. 소켓 생성

OS는 socket 메서드로 소켓을 생성할 수 있다. 이 메서드는 **소켓 디스크립터** 라는 식별자값을 반환한다. 

## 2. 소켓 접속

생성한 소켓을 서버의 소켓에 연결하기 위해 프로토콜 스택에 의뢰한다. 

이때 `connect` 메서드를 활용하며 내부적으로 **`디스크립터`**, **`IP 주소`**, **`포트 번호`** 3가지를 받는다. 

- 응답받은 소켓 디스크립터를 프로토콜 스택에 전달하며, 프로토콜 스택은 이를 바탕으로 서버의 어떤 소켓을 연결할지 판단하고 접속한다.
- IP 주소는 우리가 접근할 서버 IP 주소다. 하지만 IP 주소만으로는 소켓까지 접근하기 어려워서, 포트 번호도 함께 보낸다. (웹의 경우 80번 포트)
- 디스크립터로는 로컬 내에서 소켓을 식별하는 것이며, 포트 번호는 외부에서 소켓에 접속하기 위해 사용하는 것이다.

이러한 절차로 클라이언트는 서버의 소켓에 접속할 수 있다.  클라이언트 소켓 포트 번호는 소켓 생성 시 프로토콜 스택이 할당하고, 접속시 서버에게 통지해준다. 

## 3. 데이터 송수신

소켓에 데이터를 보낼 때, `write` 메서드는 **`디스크립터`**와 **`데이터`**, **`데이터 길이`**를 넘겨준다. 

- 데이터는 당연히 HTTP 메시지를 말한다.
- 디스크립터를 통해 소켓에게 데이터를 전달한다. 디스크립터는 소켓 식별번호라고 앞서 말했다.

데이터를 읽을 땐 `read` 메서드로 **`디스크립터`**와 **`수신 버퍼`**를 받는다. 

- 수신 버퍼로 받은 데이터를 저장한다. (어플리케이션의 메모리 영역)
- 메모리 영역에 저장한다는 것은 메세지를 어플리케이션에 건네주는 것과 동일한 것이다.

## 4. 연결 해제

`close` 메서드로 소켓 사이의 파이프를 분리하고 소켓을 말소시킨다. 

- 서버가 응답 메세지를 반환한 후 close 메서드를 실행한다.
- 클라이언트는 이를 받아서 소켓을 연결 끊기 단계로 만든다.
- 이후 read 메서드 요청이 오면 이에 대한 응답으로 데이터 대신 연결 해제 상태를 통보한다.
- 그럼 브라우저는 아 소켓 끊겼구나, 하고 close 메서드를 실행한다.

# 요약

> 💡 **데이터 송수신 동작 과정**
> - 서버와 클라이언트가 데이터를 송수신하기 위해선 connect 메서드로 소켓이 열리고 파이프가 연결돼야 한다.
> - connect 메서드는 디스크립터와 IP 주소, 포트번호를 포함한다.
> - 데이터 송신은 write 메서드에 디스크립터와 데이터, 데이터 길이를 넣어 보낸다.
> - 데이터 수신은 read 메서드로 디스크립터와 수신 버퍼를 받는다.
> - 연결 해제는 close 메서드로 파이프를 분리하고 소켓을 말소시킨다.
