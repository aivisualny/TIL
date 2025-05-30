>>SQlD 자격증 시험 대비 **최종 정리본**입니다.

>> 1과목(데이터 모델링의 이해)와 2과목(SQL 기본 및 활용)로 나누고, 
각각 **정의/종류/특징/비교/주의사항** 식으로 분류했습니다.

<br>

# 1과목. 데이터 모델링의 이해  
<Br>

## 1. 데이터 모델의 구분  
- **개념적 데이터 모델**: 현실 세계를 추상화  
- **논리적 데이터 모델**: 관계형 구조로 표현  
- **물리적 데이터 모델**: 실제 데이터베이스 구조 설계

## 2. 스키마 3계층  
- **외부 스키마**: 사용자 뷰  
- **개념 스키마**: 전체 DB의 논리적 구조  
- **내부 스키마**: 물리적 저장 구조

## 3. ERD 기호 해석  
- 실선: 식별 관계  
- 점선: 비식별 관계  
- 까마귀발: 다수  
- 동그라미: 선택

## 4. 엔터티의 분류  
- **유형**  
  - 유형 엔터티: 사원, 상품  
  - 개념 엔터티: 보험상품  
  - 사건 엔터티: 주문, 사고  
- **발생 시점**  
  - 기본 엔터티: 사원, 부서  
  - 중심 엔터티: 계약, 주문  
  - 행위 엔터티: 이력, 참여

## 5. 속성의 종류  
- 단순 속성: 쪼갤 수 없음 (사번)  
- 복합 속성: 쪼갤 수 있음 (주소)  
- 설계 속성: 설계 과정에서 추가  
- 파생 속성: 계산 가능 (이자, 재고)

## 6. 도메인  
- 속성이 가질 수 있는 값의 범위  
- 데이터 타입, 크기, 제약조건 포함

## 7. 식별자  
- **구분 기준**  
  - 주 식별자 / 보조 식별자  
  - 내부 / 외부 식별자  
  - 단일 / 복합 식별자  
  - 본질 / 인조 식별자  
- **주 식별자의 4대 특징**  
  - 유일성 / 최소성 / 불변성 / 존재성

## 8. 관계 설정  
- 관계차수: 1:1, 1:M 등  
- 관계 선택사양: 필수/선택  
- 관계 엔터티: 일반 속성 없이도 가능 (PK만 존재)

## 9. 모델링 관점  
- 데이터 중심  
- 프로세스 중심  
- 데이터-프로세스 상호작용 중심

## 10. 모델링의 특징  
- 추상화 / 단순화 / 명확화

## 11. 정규화  
- 제1정규형: 속성은 원자값  
- 제2정규형: 기본키 전체에 종속  
- 제3정규형: 일반 속성 간 종속 제거

## 12. 반정규화  
- 성능 향상을 위한 정규화의 역행  
- 특징: 입력 속도 증가, 조회 속도 감소  
- 기법  
  - 중복 컬럼 추가  
  - 파생 컬럼 추가  
  - 이력 테이블 구성  
  - PK 기반 컬럼 추가

<br>

# 2과목. SQL 기본 및 활용  

<Br>

## 1. SQL의 구분  
| 유형 | 명령어 |
| --- | --- |
| DDL | CREATE, ALTER, RENAME, DROP, TRUNCATE |
| DML | SELECT, INSERT, UPDATE, DELETE |
| DCL | GRANT, REVOKE |
| TCL | COMMIT, ROLLBACK, SAVEPOINT |

## 2. 데이터 정의  
- ALTER TABLE  
  - ADD: 컬럼 추가  
  - DROP: 컬럼 삭제  
  - MODIFY: 데이터형 변경  
- TRUNCATE TABLE  
  - DELETE보다 빠름  
  - ROLLBACK 불가

## 3. 데이터 조회  
- SELECT 사용 시 ALIAS는 GROUP BY에서 사용 불가  
- ESCAPE 문자는 특수문자 검색 시 사용  
- ORDER BY 복수 기준 가능 (`ORDER BY A, B DESC`)  

## 4. 조인  
- INNER JOIN: 공통값 기준  
- OUTER JOIN: LEFT, RIGHT, FULL  
- CROSS JOIN: ON 없이 곱집합  
- A CROSS JOIN B = A × B

## 5. 서브쿼리  
- SELECT 절: 스칼라 서브쿼리  
- FROM 절: 인라인 뷰  
- WHERE, HAVING 절: 중첩 서브쿼리

## 6. 집계 함수  
- SUM, COUNT, AVG, MIN, MAX  
- COUNT(COL1): NULL 제외  
- COUNT(*) : 전체 행 수  
- SUM: NULL 제외  
- + 연산: NULL 포함 시 결과 NULL

## 7. 분석 함수  
| 함수 | 설명 |
| ---- | ---- |
| ROW_NUMBER() | 순위 고유 부여 |
| RANK() | 동일 순위 동일값, 다음 순위 건너뜀 |
| DENSE_RANK() | 동일 순위, 다음 순위 연속 |
| NTILE(n) | n등분하여 그룹 부여 |
| LEAD(col, offset, def) | 이후 행의 값 |
| LAG(col, offset, def) | 이전 행의 값 |
| RATIO_TO_REPORT(col) | 그룹 내 비율 (0~1)

## 8. GROUP BY 응용  
| 구문 | 그룹핑 방식 |
| ---- | ------------ |
| ROLLUP(A, B) | A, B → A → 전체 |
| CUBE(A, B) | A, B → A → B → 전체 |
| GROUPING SETS(A, B) | A로, B로 별도 그룹핑 |

## 9. NULL 처리 함수  
- IFNULL(a, b) = NVL(a, b)  
- NULLIF(a, b): 같으면 NULL  
- COALESCE(a, b, c...): NULL 아닌 첫 번째 값

## 10. 트랜잭션 특성 (ACID)  
- 원자성 (Atomicity)  
- 일관성 (Consistency)  
- 격리성 (Isolation)  
- 영속성 (Durability)

## 11. 권한 부여  
- `GRANT 권한 ON 테이블 TO 사용자`  
- 권한 묶음은 ROLE로 구성

## 12. 기타 SQL 문법  
- IS NOT NULL: NULL만 판별  
- CUBE: 가능한 모든 부분집합  
- GROUPING SETS: 명시된 조합만  
- CONNECT BY 계층 쿼리 함수  
  - LEVEL, CONNECT_BY_ISLEAF, CONNECT_BY_ROOT, SYSTEM_CONNECT_BY_PATH, CONNECT_BY_ISCYCLE

## 13. 주의사항 요약  
- VIEW 특징: 독립성 / 편리성 / 보안성  
- CHAR vs VARCHAR2  
  - CHAR: 고정 길이, 공백 채움  
  - VARCHAR2: 가변 길이  
- 연산 우선순위  
  - 산술 → 연결(||) → 비교 → IN, LIKE → NOT → AND → OR  
- ANY 연산자  
  - `A > ANY(B)`는 B 중 최소값보다 크면 TRUE


직전 꿀팁들

1. GROUB BY ㅇ ORDER BY ㅁ 에서 ㅁ는 ㅇ안에 없으면 사용 불가
2. ORDER BY 2 이렇게 숫자를 쓰면 N번 째 칼럼을 뜻하기때문에, 그 칼럼이 SELECT문 안에 없으면 사용 불가
