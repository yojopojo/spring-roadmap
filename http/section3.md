# HTTP 기본


## HTTP(HyperText Transfer Protocol)란?
HTTP 메시지를 통해 다양한 데이터를 전송할 수 있음
- HTML, TEXT
- 이미지, 음성, 영상, 파일
- JSON, XML(API)
- 서버 간 통신에도 HTTP를 사용
- 실무에서도 TCP 등을 직접 사용하기보다 HTTP를 사용하는 경우가 많음


## HTTP 역사
- HTTP/0.9(1991년): GET 메서드만 지원, HTTP 헤더 없음
- HTTP/1.0(1996년): 메서드, 헤더 추가
- HTTP/1.1(1997년): 가장 많이 사용되는 버전
    - RFC2068(1997) -> RFC2616(1999) -> RFC7230~7235(2014)
- HTTP/2(2015년): 성능개선
- HTTP/3(~진행중): TCP 대신 UDP 사용, 성능 개선


## 기반 프로토콜
- TCP: HTTP/1.1, HTTP/2
- UDP HTTP/3
- 개발자 도구의 Network -> Protocol 항목에서 확인 가능

## HTTP 특징
### 클라이언트 -  서버가 분리된 구조
- Request - Response 구조
- 클라이언트는 서버에 요청을 보내고, 응답을 대기
- 서버가 요청에 대한 결과를 만들어서 응답
- 비즈니스 로직, 데이터는 서버에, UI와 사용성은 클라이언트에 집중시킴 -> 독립적인 진화가 가능해짐

### 무상태 프로토콜(Stateless)
- 단어 그대로 상태를 저장하지 않음 -> 필요한 데이터가 다 담긴 채로 요청이 옴
- Stateless의 경우 응답 서버가 바뀌어도 되므로 서버 증설이 쉬움(Scale-out)
- 한계점이 있음(로그인 등 상태를 유지해야 하는 경우)
- 상태 유지(Stateful)는 꼭 필요한 경우에 최소한으로만 사용함 -> 브라우저 쿠키, 서버 세션 등을 사용

### 비연결성(Connectionless)
- 클라이언트가 아무런 작업을 하지 않아도 연결이 유지되는 경우 불필요한 서버 자원 소모가 일어남
- 연결을 유지하지 않는 경우, 필요할 때만 자원을 사용하기 때문에 자원 소모를 최소한으로 줄일 수 있음
- 많은 이용자가 동시에 사용하더라도, 실제 서버에서 동시에 처리해야 하는 요청의 수는 매우 작음 -> 서버의 자원을 효율적으로 사용할 수 있음
- TCP/IP 연결을 매번 새로 맺어야 하는 단점(시간이 더 오래 걸림)
- HTTP 지속연결(Persistent Connections)을 통해 단점을 상쇄 -> 한 페이지에 대한 자원을 모두 받을 때까지 연결을 유지


## 실무에서의 Stateless
선착순 이벤트, 많은 이용자가 동시요청하는 경우 등, 무상태여야 해결하기 수월한 문제가 많으므로 최대한 Stateless하게 설계해야 함


## HTTP 메시지

### HTTP 메시지 구조
- start-line: 시작 라인
- header
    - [필드명:OWS필드값OWS] 구조
    - OWS: 띄어쓰기 허용
    - 필드명은 대소문자 구분 없음
    - HTTP 전송에 필요한 모든 부가정보가 들어있음
    - 표준 헤더의 종류는 매우 다양함
    - 필요 시 임의의 헤더 추가 가능
- empty line: 공백라인(CRLF)
- message body
    - 실제 전송할 데이터
    - HTML 문서, 이미지, 영상, JSON 등 byte로 전송 가능한 모든 데이터

### 요청 메시지
- start-line
    - HTTP 메서드
        - GET(리소스 조회), POST(요청 내역 처리), PUT, DELETE 등
        - 서버가 수행해야 할 동작 지정
    - 요청 대상
        - [절대경로?쿼리] 구조
    - HTTP 버전
- header
- empty line
- message body

### 응답 메시지
- start-line
    - HTTP 버전
    - HTTP 상태 코드
        - 200: 성공
        - 400: 클라이언트 요청 오류
        - 500: 서버 내부 오류
    - 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명
- header
- empty line
- message body: 데이터