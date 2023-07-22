# interview-study

# 네트워크
## SSL
- SSL(Secure Sockets Layer, 보안 소켓 계층) : 웹사이트와 브라우저 사이의 전송되는 데이터를 암호화하여 인터넷 연결을 보호하기 위한 표준 기술
- HTTP 요청 메시지 전체를 암호화함
- 대칭키 암호화 : 같은 키로 암호화함, AES 알고리즘
- 공개키 암호화 : 서로 다른 키(public key, private key)를 사용하여 암호화함, RSA 알고리즘

## Ngnix와 apache
- apache
    - 각 클라이언트 요청마다 새로운 프로세스 혹은 쓰레드를 생성함 -> 메모리 사용량이 상대적으로 높음
    - 클래식 프로세스 또는 쓰레드 기반 아키텍처
    - 요청 당 커넥션 프로세스 생성
    - PREFORK로 미리 프로세스풀을 만듬
    - keep-alive = 커넥션 재사용 -> 요청 더욱 많아짐 -> C10K 문제(커넥션이 10000개 이상 생성 불가) 발생
    - 동시에 처리하는 프로세스가 많을 수록 컨텍스트 스위칭을 많이하기 때문에 메모리가 부족함
    - 호환성과 확장성이 좋음
- nginx
    - 단일 프로세스 또는 쓰레드에서 많은 연결을 처리하며 비동기적 처리를 하여 메모리 사용을 효율적으로 관리함
    - 이벤트 기반 아키텍처
    - 웹서버, 리버스 프록시, 로드밸런서, 이벤트 기반 구조
    - apache 서버의 약점을 보완하기 위해 탄생
    - master process, worker process 기반 
    - worker process에 지정된 소켓 배정, 프로세스가 쉬지 않고 일함, Event(커넥션 형성, 커넥션 제거, 새로운 요청처리)가 큐에 비동기 대기, 오래 걸리는 요청들은 따로 쓰레드풀에 배정
    - 컨텍스트 스위칭을 줄임, 커넥션양 100~1000배 증가, 속도 상승
    - 로드밸런서 요청 분산
    - NGINX 서버 추가가 쉬움

# 데이터베이스
## 기본 용어
- Entity(엔티티) : 여러개의 속성을 지닌 명사
- Relation(릴레이션) : 데이터베이스에서 정보를 구분하여 저장하는 기본 단위, RDB에서는 테이블이라고 함
- Attribute(속성) : 릴레이션에서 관리하는 구체적이며 고유한 이름을 갖는 정보(필드, 컬럼), 릴려이션의 각 열
- Domain(도메인) : 릴레이션에 포함된 각각의 속성이 가질 수 있는 값의 집합(성별 속성의 {남, 여}가 도메인임)
- Tuple(튜플) : 릴려이션의 각 행, 레코드, 로우
- RDB : 레코드 < 릴레이션 < 데이터베이스
- NoSQL : 도큐먼트 < 컬렉션 < 데이터베이스

## CHAR 와 VARCHAR의 차이 
- CHAR(10)으로 선언하면 10글자에 해당하는 바이트를 무조건 고정해서 두지만 VARCHAR(10)으로 선언하면 3글자 등록시 3글자에 해당하는 바이트 + 1바이트만을 사용함
- CHAR의 경우 검색에 유리하며 VARCHAR는 검색을 별로 하지 않고 유동적인 길이를 가진 데이터에 적합함

## 키
- Primary Key(기본키, PK) : 유일성과 최소성을 만족하는 속성
    - 자연키 : PK 설정 시 고려하는 키로 주민번호, 이름, 이메일, 휴대번호 등 언젠가는 변할 수 있는 키이다.
    - 인조키 : DB에서 인위적으로 생성하는 키로 ORACLE의 sequence, MySQL의 auto_increment가 있다. 
    - 주민번호 같은 것은 만에하나 바뀔 가능성이 있으므로 대부분 인조키를 PK로 잡는 것을 추천한다.
- Foreign Key(외래키, FK) : 다른 테이블의 기본키를 그대로 참조하는 값
- Candidate Key(후보키) : 유일성과 최소성을 만종하는 키
- Alternate Key(대체키) : 후보키가 2개 이상일 경우 기본키로 지정하고 남은 키
- Super Key(슈퍼키) : 각 레코드를 유일하게 식별할 수 있는 유일성을 가진 키

## 무결성
- 데이터의 정확성, 일관성, 유효성을 유지하는 것

- 개체 무결성 : 기본키는 빈 값을 허용하지 않음
- 참조 무결성 : 서로 참조 관계에 있는 두 테이블의 데이터는 항상 일관된 값을 유지해야함
- 고유 무결성 : 특정 속성에 대해 고유한 값을 가지도록 주어진 경우 그 속성값은 모두 고유한 값을 가짐
- NULL 무결성 : 특정 속성에 NULL이 올 수 없는 조건의 경우 그 속성값은 NULL이 될 수 없음

## 정규화
- 데이터베이스의 설계를 효율적이고 유연하며 일관성 있게 만드는 것
- 중복을 최소화하고 데이터를 적절한 테이블로 분리하여 데이터의 논리적 구조를 개선하여 무결성, 일관성, 보안성 등을 강화하고 데이터베이스를 최적화함

## 트랜잭션
### ACID
- Atomicity(원자성) : all or nothing, 트랜잭션에 관련된 일은 모두 수행되거나 수행되지 않아야함
    - commit(커밋) : 쿼리가 성공적으로 처리되었다고 확정하는 명령어, 변경 내용 영구 저장
    - rollback(롤백) : 트랜잭션으로 처리한 하나의 묶음 과정을 일어나기 전으로 돌리는 일
    - 커밋과 롤백 덕에 데이터의 무결성이 보장됨
- Consistency(일관성) : 허용된 방식으로만 데이터를 변경해야 함, 데이터베이스에 기록된 모든 데이터는 여러가지 조건, 규칙에 따라 유효함을 거쳐야 함
- Isolation(격리성) : 트랜잭션 수행시 서로 끼어들지 못함, 서로 다른 트랜잭션은 각각에 영향을 줄 수 없음
- Durability(지속성) : 성공적으로 수행된 트랜잭션은 영원히 반영되어야 함

### Isolation Level(격리 수준)

- READ_UNCOMMITTED(커밋되지 않은 읽기) : 트랜잭션의 변경내용이 커밋과 롤백상관없이 다른 트랜잭션에서 조회 가능, 더티리드 발생
- READ_COMMITTED(커밋된 읽기) : 트랜잭션의 변경내용이 커밋되어야만 다른 트랜잭션에서 조회가능, 반복가능하지 않은 조회 발생
- REPEATABLE_READ(반복 가능한 읽기) : 트랜잭션이 시작되기 전의 커밋된 내용만 조회, 데이터 부정합 발생, 팬텀 리드 발생
- SERIALIZABLE(직렬화 가능) : 트랜잭션에서 이 레코드를 변경하지 못하게 잠금 설정, 동시처리 능력이 다른 격리 수준 보다 떨어지고 성능 저하가 발생, 격리성은 좋지만 동시성은 매우 안좋음
- 팬텀 리드 = 한 트랜잭션 내에서 동일한 쿼리를 보냈을 때 조회 결과가 다른 경우
- 반복가능하지 않은 조회 = 한 트랜잭션 내의 같은 행에 두번이상 조회가 발생했는데 그 값이 다른 경우
- 더티 리드 = 다른 트랜잭션에 의해 수정되었지만 아직 커밋되지 않은 행의 데이터를 읽을 수 있는 경우

## 인덱스
- 인덱스가 효율적인 이유는 효율적인 단계를 거쳐 모든 요소에 접근할 수 있는 균형잡힌 트리구조와 트리깊이의 대수확장성 때문임
- 클러스텨형 인덱스 : 테이블의 데이터 레코드를 직접 정렬하여 저장
- 세컨더리 인덱스 : 테이블의 다른 열들을 기반으로 데이터 레코드의 물리적 위치를 찾음
    - MySQL : 클러스터형 인덱스와 세컨더리 인덱스가 있음, 클러스터형 인덱스는 테이블당 하나만 가능
    - PK -> 클러스터형 인덱스
    - UK, Not Null -> 클러스터형 인덱스
    - create index -> 세컨더리 인덱스
- 하나의 인덱스만 만든다면 클러스터형 인덱스가 성능이 좋음
- 세컨더리 인덱스는 보조 인덱스로 여러개의 필드 값을 기반으로 쿼리를 많이 보낼 때 생성해야하는 인덱스
- 복합 인덱스 생성시 순서가 있고 생성 순서에 따라 인덱스 성능이 달라짐
- 같음, 정렬, 다중 값, 카디널리티 순으로 생성해야 함
    - 같음 : '==' 이나 'equal' 쿼리가 있으면 제일 먼저
    - 정렬 : order by 쿼리가 있으면 그 다음
    - 다중 값 : '>' 이나 '<' 등 많은 값을 출력하는 것
    - 카디널리티 : 유니크함 정도, age와 email이 있다면 email이 먼저 인덱스가 생성되어야 함

- MySQL에서는 EXPLAIN을 통해 쿼리 테스트 가능
```
EXLPLAIN
SELECt * FROM T1
```

# 자바
## 자바8
- 람다식, 스트림 API, 인터페이스 디폴드와 정적메서드, Optional 클래스, 새로운 날짜 API(java.time)

## 자바11
- 지연성 클래스 = var로 선언된 변수에 지연 초기화 지원
- 새로운 HTTP 클라이언트 API(HTTP 클라이언트, 비동기 체이닝)
- for문 등 더 많은 곳에서 var 사용 가능
- Epsilon 가비지 컬렉터 추가

## 스트림의 장점
1. 선언형 프로그래밍 -> 코드의 가독성 향상
2. 내부 반복 = 반복 코드를 직접 작성하지 않아도 됨
3. 지연 평가 = 필요할 때만 요소를 처리하므로 모든 요소를 메모리에 불러오지 않고도 효율적으로 처리 가능
4. 병렬처리 내부 요소들을 병렬적으로 처리해 멀티코어 CPU를 활용한 빠른 처리가 가능함

# JWT
- JWT의 가장 큰 장점은 self descriptionable 하다는 것임
- 토큰 자체로 인증을 증명하여 굳이 데이터베이스 조회, session 조회 없이 인증이 가능함
- JOSE(Json Object Signing and Encryption) : 당사자간 claim을 안전하게 전송하기 위한 프레임워크
- 무결성 = 완전성, 정확성, 일관성
- JWS와 JWE를 사용하여 보안성을 올림
- JWS를 통해 무결성을 올림
- JWE를 통해 claim을 암호화하여 기밀성을 올림
- JWT secret 알고리즘 = HMAC 512 사용(SHA 512를 해시함수로 구현, 데이터 무결성 검증)
- 인증 상태를 서버에 저장하지 않아 메모리 사용에 효율적임
- 필수정보를 스스로 가지고 있어 DB 조회가 필요하지 않음

## 리프레시 토큰 사용 이유
- 액세스 토큰은 유효기간을 길게하면 보안 문제가 있고 짧게 하면 사용자들에게 좋지않은 경험을 주게됨
- 그래서 리프레시 토큰을 사용하여 액세스 토큰이 만료되었을 때 재발급을 받기위해 만들어짐
- 리프레시 토큰은 보안을 높이기 위해 DB에 저장하고 스크립트 공격 등을 대비하여 js를 통해 접근불가한 HttpOnly 속성과 secure 속성을 쿠키에 함께 부여하게 됨

# Security
## OncePerRequestFilter
- spring security 필터 체인에서 요청당 한번만 실행되는 필터를 구현하는데 사용

## 인증처리 순서
1. 해당 api endpoint에 username과 password를 작성하여 요청함
2. 요청받은 후 서비스 레이어에서 AuthenticationManagerBuilder를 통해 AuthenticationManager 객체를 가져옴
3. username과 password를 이용하여 UsernamePasswordAuthenticationToken을 만들어 AuthenticationManager의 authenticate 메서드에 인자로 넣음
4. 내부적인 동작을 통해 AuthenticationProvider에서 UserDetailsService의 loadByUsername 메서드를 호출하게 되고 loadByUsername에서 실제 DB에 접속하여 해당 username에 해당하는 엔티티를 불러오게됨
5. 해당 엔티티를 불러와 UserDetails의 구현체를 생성하여 리턴하게됨
6. 내부적으로 UserDetails와 UsernamePasswordAuthenticationToken을 비교하여(username, password 검증) 인증에 성고하면 SecurityContextHolder에 Authentication 객체를 set하여 완료하게 되고 아닐시 에러를 던지게 됨
7. 성공적으로 인증이 완료되면 액세스 토큰(json body)과 리프레시 토큰(cookie)을 생성하여 리턴하게됨

## JWT 헤더 검증
- spring security filter chain에 OncePerRequestFilter를 구현한 JwtAuthorizationFilter를 구현하게 됨
- 해당 필터에서 Bearer형식의 JWT가 해당 Authorization Header 값에 들어있는지 유효성 검증을 하게 됨
- 검증 완료시 SecurityContextHolder에 Authentication 객체를 만들어 완료하게되고 검증 실패시 에러를 던지게 됨

# Spring
## SpringBoot의 이점
- 내장 톰캣 지원
- 라이브러리 의존성 관리
- starter 종속성 제공

## @WebMvcTest
- 웹애플리케이션 계층의 테스트를 작성할 때 사용
- 주로 Controller와 관련된 테스트
- Spring MVC 관련 테스트 기능만 로드하므로 웹계층에 관련된 빈들만 생성됨
- 실제 웹서버가 실행되지 않기 때문에 속도가 빠르며 다른 레이어에 대한 빈들은 로드하지 않기 때문에 MockBean을 주입하여 사용되어야 함

## @SpringBootTest
- 전체 애플리케이션 컨텍스트를 로드하여 통합 테스트를 작성할 때 사용
- 실제 모든 빈을 로드하여 테스트함

# 동시성 다루기
## synchronized 키워드
- 애플리케이션에 종속적이라 다중 서버 환경에서 사용 불가

## pessimistic lock
- 실제 select for update 쿼리가 나감
- 동시성 이슈가 빈번히 일어나는 부분에 사용하면 optimistic lock보다 성능이 좋음

## optimistic lock
- JPA의 @Version을 이용한 방법
- 별도의 lock을 실제로 잡지는 않아 재시도 처리를 따로 해줘야함(Facade 패턴)
- 동시성 이슈가 빈번하게 일어나지 않을 경우에 사용하는 걸 권장함

## mysql named lock
- 분산락의 일종
- transaction 종료시 lock 해제와 세션 관리 필요
- 실제 사용시 구현이 복잡할 수 있음(별도의 데이터소스, jdbc 셋팅 필요)
- select get_lock, select release_lock

## redis lettuce
- setnx 명령어를 활용한 분산락
- 계속해서 락획득을 시도하는 로직과 재시도 로직이 필요함
- 재시도가 필요하지 않을 경우 사용

## redis reddison
- pub-sub 기반의 lock 구현
- 락 획득 재시도를 기본으로 제공하여 별도 로직이 필요하지 않음
- 별도 라이브러리 설치 필요
- lettuce에 비해 구현이 복잡함