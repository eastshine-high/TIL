# Transaction

**트랜잭션(Transaction)은 업무 처리를 위한 논리적인 작업 단위다.** 작업의 논리적 단위가 단일 연산이 아닐 수 있다. 즉, 하나의 트랜잭션이 두 개 이상의 갱신 연산일 수 있다. 은행의 "계좌이체" 트랜잭션을 예로 들면, 하나의 예금 계좌에서 인출하여 다른 예금 계좌에 입금하는 일련의 작업을 하나의 단위로 수행해야 한다.

데이터를 일관성 있게 처리하려면 트랜잭션에 속한 두 개 이상의 갱신 연산을 동시에 실행할 수 잇어야 하는데, 불행히도 이는 불가능한 일이다. 따라서 DBMS는 차선책을 사용한다. 즉, 여러 개의 갱신 연산이 하나의 작업처럼 전부 처리되거나 아예 하나도 처리되지 않도록(All or Nothing) 동시 실행을 구현한다.

## 트랜잭션의 특징(ACID)

- 원자성(Atomicity) : **트랜잭션은 더 이상 분해가 불가능한 업무의 최소단위**이므로, 전부 처리되거나 아예 하나도 처리되지 않아야 한다.
- 일관성(Consistency) : 일관된 상태의 데이터베이스에서 하나의 트랜잭션을 성공적으로 완료하고 나면 그 데이터베이스는 여전히 일관된 상태여야 한다. 즉, 트랜잭션 실행의 결과로 데이터베이스 상태가 모순되지 않아야 한다.
- 격리성(Isolation) : 실행 중인 트랜잭션의 중간결과를 다른 트랜잭션이 접근할 수 없다.
- 영속성(Durability) : 트랜잭션이 일단 그 실행을 성공적으로 완료하면 그 결과는 데이터베이스에 영속적으로 저장된다.

## 트랜잭션 격리성

트랜잭션의 격리성은, 일관성과 마찬가지로 Lock을 강하게 오래 유지할수록 강화되고, Lock을 최소화할수록 약화된다.

### 낮은 단계의 격리성 수준에서 발생할 수 있는 현상들

**Dirty Read**

다른 트랜잭션에 의해 수정됐지만 아직 커밋되지 않은 데이터를 읽는 것을 말한다. 변경 후 아직 커밋되지 않은 값을 읽었는데 변경을 가한 트랜잭션이 최종적으로 롤백된다면 그 값을 읽은 트랜잭션은 비일관된 상태에 놓이게 된다.

**Non-Repeatable Read**

한 트랜잭션 내에서 같은 쿼리를 두 번 수행했는데, 그 사이에 다른 트랜잭션이 값을 수정 또는 삭제하는 바람에 두 쿼리 결과가 다르게 나타나는 현상을 말한다.

예시) 어떤 계좌에서 인출 가능한 금액을 조회한 뒤에 인출 가능한 금액을 출금하려는 사이에, 다른 트랜잭션이 먼저 이 계좌에서 출금을 했을 경우.

```SQL
-- (TX1)
SELECT 잔고 into :balance
FROM 계좌
WHERE 계좌번호 = 123;

-- (TX2)
UPDATE 계좌
SET 잔고 = 잔고 - 50000
WHERE 계좌번호 = 123

COMMIT;

-- (TX1)
UPDATE 계좌
SET 잔고 = 잔고 - 10000
WHERE 계좌번호 = 123
AND 잔고 >=10000;

If sql%rowcount = 0 THEN
	alert('잔고가 부족합니다');
END IF;

COMMIT;
```

**Phantom Read**

한 트랜잭션 내에서 같은 쿼리를 두 번 수행했는데, 첫 번째 쿼리에서 없던 유령(Phantom) 레코드가 두 번째 쿼리에서 나타나는 현상을 말한다.

예시)

```sql
-- (TX1)
INSERT INTO 지역별고객
SELECT 지역별고객
FROM 고객
GROUP BY 지역;

-- (TX2)
INSERT INTO 고객 (고객번호, 이름, 지역, 연령대 ...)
VALUES( :a, :b, :c, :d, ...)

COMMIT;

-- (TX1)
INSERT INTO 연령대별고객
SELECT 연령대, COUNT(*)
FROM 고객
GROUP BY 연령대;

COMMIT;
```

TX1 트랜잭션이 지역별고객과 연령대별고객을 연속해서 집계하는 도중에 새로운 고객이 TX2 트랜잭션에 의해 등록되었다. 그 결과, 지역별고객과 연령대별고객 두 집계 테이블을 통해 총고객수를 조회하면 서로 결과 값이 다른 상태에 놓이게 된다.

### 트랜잭션 격리성 수준

ANSI/ISO SQL 표준(SQL92)에서 정의한 4가지 트랜잭션 격리성 수준(Transaction Isolation Level)은 다음과 같다. 트랜잭션 격리성 수준은 ISO에서 정한 분류 기준일 뿐이며, 모든 DBMS가 4가지 레벨을 다 지원하지는 않는다.

**Read Uncommitted**

트랜잭션에서 처리 중인 아직 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용한다.

**Read Commited**

**트랜잭션이 커밋되어 확정된 데이터만 다른 트랜잭션이 읽도록 허용함으로써 Dirty Read를 방지해준다.** 커밋된 데이터만 읽더라도 Non-Repeatable Read와 Phantom Read 현상을 막지는 못한다. 읽는 시점에 따라 결과가 다를 수 있다는 것이다.

**Repeatable Read**

트랜잭션 내에서 쿼리를 두 번 이상 수행할 때, 첫 번째 쿼리에 있던 레코드가 사라지거나 값이 바뀌는 현상을 방지 해 준다. 이 트랜잭션 격리성 수준이 Phantom Read 현상을 막지는 못한다. **첫 번째 쿼리에서 없던 새로운 레코드가 나타날 수 있다**는 것이다.

**Serializable Read**

트랜잭션 내에서 쿼리를 두 번 이상 수행할 때, 첫 번째 쿼리에 있던 레코드가 사라지거나 값이 바뀌지 않음은 물론 새로운 레코드가 나타나지도 않는다.

각 격리성 수준에서 나타날 수 있는 현상을 요약하면 다음과 같다.

| 레벨 | Dirty Read | Non-Repeatable Read | Phantom Read |
| --- | --- | --- | --- |
| Read Uncommitted | O | O | O |
| Read Committed | X | O | O |
| Repeatable Read | X | X | O |
| Serializable Read | X | X | X |

**대부분의 DBMS가 Read Commited를 기본 격리성 수준으로 채택**하고 있으므로 Dirty Read가 발생할까 걱정하지 않아도 되지만, Non-Repeatable Read, Phantom Read 현상에 대해선 세심한 주의가 필요하다.

다중 트랜잭션 환경에서 DBMS가 제공하는 기능을 이용해 동시성을 제어하려면 트랜잭션 시작 전에 명시적으로 Set Transaction 명령어를 수행하기만 하면 된다. 아래는 트랜잭션 격리성 수준을 Serializable Read로 상향 조정하는 예시다.

```sql
set transaction isolation level read serializable;
```

### 주의사항

트랜잭션 또한 DBMS의 커넥션과 동일하게 꼭 필요한 최소의 코드에만 적용하는 것이 좋다. 이는 프로그램 코드에서 트랜잭션의 범위를 최소화하라는 의미다. 예)
[트랜잭션 격리 수준과 잠금](https://github.com/eastshine-high/til/blob/main/relational-database/my-sql/innodb-lock.md#%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EA%B2%A9%EB%A6%AC-%EC%88%98%EC%A4%80%EA%B3%BC-%EC%9E%A0%EA%B8%88)

---

**참조**

<한국데이터진흥원, SQL 전문가 가이드>
