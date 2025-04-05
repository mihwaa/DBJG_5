## 14.5 Flow Control Functions
```
CASE 표현식의 반환 타입은 통합된 타입에 따라 결정된다.
모든 타입이 숫자일 경우 → 결과도 숫자 타입 같은 방식이다.
```
```
IF(expr1, expr2, expr3)
1이 트루면 2 반환, 아니면 3 반환
```
```
IFNULL(expr1, expr2)
1이 NULL이면 1, 아니면 2 반환

NULLIF(expr1, expr2)
1=2면 NULL, 아니면 1반환
CASE WHEN expr1 = expr2 THEN NULL ELSE expr1 END 와 동일하다.
```

## 14.4.2 Comparison Functions and Operators
| 연산자 / 함수 | 설명 |
|---------------|------|
| `>`           | 크다 (Greater than) |
| `>=`          | 크거나 같다 (Greater than or equal) |
| `<`           | 작다 (Less than) |
| `<=`          | 작거나 같다 (Less than or equal) |
| `=`           | 같다 (Equal) |
| `<>`, `!=`    | 같지 않다 (Not equal) |
| `<=>`         | NULL-safe 동등 비교 |
| `BETWEEN ... AND ...` | 값이 특정 범위 내에 있는지 확인 |
| `NOT BETWEEN ... AND ...` | 값이 범위 밖에 있는지 확인 |
| `IN()`        | 값이 집합에 포함되는지 확인 |
| `NOT IN()`    | 값이 집합에 포함되지 않는지 확인 |
| `EXISTS()` / `NOT EXISTS()` | 서브쿼리 결과 존재 여부 확인 |
| `IS NULL` / `IS NOT NULL` | NULL 여부 확인 |
| `IS`, `IS NOT` | Boolean 비교 (TRUE, FALSE, UNKNOWN) |
| `ISNULL()`    | 값이 NULL인지 확인 (1: NULL, 0: NOT NULL) |
| `COALESCE()`  | NULL이 아닌 첫 번째 값 반환 |
| `NULLIF()`    | 두 값이 같으면 NULL, 아니면 첫 번째 값 반환 |
| `GREATEST()`  | 인자 중 가장 큰 값 반환 |
| `LEAST()`     | 인자 중 가장 작은 값 반환 |
| `STRCMP()`    | 문자열 비교 (정렬 순서에 따라 -1, 0, 1) |
| `INTERVAL()`  | 값이 몇 번째 구간에 속하는지 반환 |
| `LIKE` / `NOT LIKE` | 문자열 패턴 비교 |

---

## ✅ 비교 결과 값
- 대부분의 비교 연산자는 **1 (TRUE)**, **0 (FALSE)** 또는 **NULL**을 반환합니다.
- 문자열 ↔ 숫자 간 비교 시 자동 형 변환 발생
---



1번
```
select 
 CASE 
    WHEN A+B <= C OR B+C <= A OR A+C <= B THEN 'Not A Triangle'
    WHEN A=B AND B=C AND C=A THEN 'Equilateral'
    WHEN A<>B AND B<>C AND C<>A THEN 'Scalene'
    WHEN A=B AND A<>C THEN 'Isosceles'
    WHEN B=C AND A<>B THEN 'Isosceles'
    WHEN A=C AND A<>B THEN 'Isosceles' END AS TYPE from TRIANGLES

CASE문에서 앞에 건 조건부터 걸려서, NOT A TRIANGLE을 마지막에 두니까 제대로 정답이 출력이 안 됐다.
```

2번
```
SELECT NAME FROM Customer
WHERE referee_id != 2 or referee_id is null;

null은 !=2같은 연산자 계산할 때 항상 조심해야 한다.
```
