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
SELECT * FROM T1
```
