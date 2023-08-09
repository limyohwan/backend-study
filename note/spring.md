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
