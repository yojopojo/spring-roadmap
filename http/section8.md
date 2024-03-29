# HTTP 헤더2 - 캐시와 조건부 요청

### 캐시가 없다면?
- 데이터가 변경되지 않아도 계속 네트워크를 통해 데이터를 다운로드 받아야 함
- 인터넷 네트워크는 매우 느리고 비쌈
- 브라우저 로딩 속도가 느림
- 좋지 않은 사용자 경험

### 캐시가 있다면?
- 캐시 가능 시간동안 네트워크를 사용하지 않아도 됨
- 비싼 네트워크 사용량을 줄일 수 있음
- 브라우저 로딩 속도 빠름
- 좋은 사용자 경험

### 캐시 시간 초과
- 캐시 유효 시간이 초과하면 다음 두 가지 상황을 겪게 됨
    - 서버에서 기존 데이터를 변경한 경우
    - 서버에서 기존 데이터를 변경하지 않은 경우


## 검증 헤더와 조건부 요청
캐싱한 원본 데이터가 변경되지 않은 경우, 불필요하게 또 데이터를 가져올 필요가 없음 -> 검증 헤더로 확인하여 필요한 경우에만 데이터 받아옴
- 검증 헤더 추가
    - Last-Modified 헤더를 통해 기준 시간 이후에 수정된 경우에만 데이터 가져옴
- 조건부 요청
    - If-Modified-Since 를 통해 기준일을 보내서 데이터를 받아올지, 말지 결정함
- 캐시 유효 시간이 초과해도 서버의 데이터가 갱신되지 않으면 304 + 헤더 메타 정보만 응답함(바디 없음)
- 클라이언트는 서버가 보낸 응답 헤더 정보로 캐시의 메타 정보를 갱신
- 클라이언트는 캐시에 저장된 데이터 재활용
- 결과적으로 네트워크 다운로드가 발생하긴 하지만, 용량이 적은 헤더 정보만 다운로드함 -> 실용적인 해결책!

### 검증 헤더
- 캐시 데이터와 서버 데이터가 같은지 검증
- Last-Modified, ETag

### 조건부 요청 헤더
- 검증 헤더로 조건에 따른 분기
- <b>If-Modified-Since: Last-Modified</b> 사용
    - 1초 미만 단위로 캐시 조정이 불가능
    - 날짜 기반 로직을 사용 -> 날짜는 다르지만 결과는 같은 경우가 있음
    - 서버에서 별도로 캐시 로직을 관리하려는 경우 불리함
        - ex) 스페이스, 주석처럼 크게 영향을 주지 않는 경우
- <b>If-None-Match: ETag</b> 사용
    - ETag(Entity Tag)
        - 캐시용 데이터에 임의의 버전을 달아둠
        - 데이터가 변경되면 태그를 변경함(Hash 재생성)
    - 캐시 제어 로직을 서버에서 완전히 관리할 수 있음
    - 클라이언트는 캐시 매커니즘을 모름
- 조건이 만족하면 200 OK
- 조건이 만족하지 않으면 304


## 캐시 제어 헤더
- Cache-Control: 캐시 제어
    - 캐시 지시어(directives) 사용
        - max-age: 캐시 유효 시간(초단위)
        - no-cache: 데이터는 캐시 가능하나, 항상 오리진 서버에 검증 후 사용
        - no-store: 데이터에 민감한 정보가 있으므로 저장 불가(메모리에서 사용 후 최대한 빨리 삭제)
        - must-revalidate: 캐시 만료 후 조회 시 오리진 서버에 검증해야 하며, 원 서버 접근 실패 시 반드시 오류(504)가 발생해야 함(단, 캐시 유효 시간이라면 캐시 사용)
        - public
        - private(default)
        - s-maxage: 프록시 캐시에만 적용되는 max-age
        - Age: 60(HTTP 헤더): 오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)
- Pragma: 캐시 제어(하위 호환)
    - no-cache처럼 동작
- Expires: 캐시 유효기간(하위 호환)
    - 캐시 만료일을 정확한 날짜로 지정
    - max-age와 함께 사용하면 무시됨


## 프록시 캐시
오리진 서버가 멀리 있는 경우 프록시 캐시 서버를 두어 빠르게 응답할 수 있도록 함<br>
프록시 캐시 서버에 저장되는 것을 public, 개인이 저장하는 걸 private cache라고 함<br>


## 캐시 무효화
확실한 캐시 무효화가 필요한 경우 다음과 같은 내용을 모두 작성하면 됨
- Cache-Control: no-cache, no-store, must-revalidate
- Pragma: no-cache

오리진 서버가 접근 불가 상태인 경우 no-cache는 에러 혹은 200을 응답