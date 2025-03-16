# 0주차 내용 정리  

## 15.2.15. Subqueries
 서브쿼리는 select 안에 또다른 select이다.  
 서브쿼리의 장점은  각 부분을 독립할 수 있게 해서 복잡한 조인, 유니온보다 읽기 쉽다.  
 서브쿼리는 다음의 값들을 반환할 수 있다
 ```
스칼라(단일 값)
행(하나의 행)
열(하나의 열)
테이블 (여러 행과 여러 열)
```
또한 단순 조회 뿐 아닌 데이터 조작어에도 서브쿼리를 활용할 수 있다.  

## 15.2.15.2 Comparisons Using Subqueries

### 1. 서브쿼리의 일반적인 형태
```
비서브쿼리 피연산자 comparison_operator (서브쿼리)
... WHERE 'a' = (SELECT column1 FROM t1);
```
### 2. like 연산자와 서브쿼리 (패턴 일치 검사)
```
비서브쿼리 피연산자 LIKE (서브쿼리)
```
### 3. 서브쿼리를 활용해야만 하는 경우
```
JOIN으로 활용할 수 없는 경우가 있다
SELECT * FROM t1
  WHERE column1 = (SELECT MAX(column2) FROM t2);
이를 JOIN으로 처리하면 다중 행이 반환될 가능성이 생긴다.
```

 ## 15.2.15.3 Subqueries with ANY, IN, or SOME 
 
 서브쿼리를 이처럼 활용해 보자.
```
피연산자 비교 연산자 ANY (서브쿼리)
피연산자 IN (서브쿼리)
피연산자 비교 연산자 SOME (서브쿼리)
```
ANY: 서브쿼리에서 반한된 값 중 하나라도 조건을 만족하면 TRUE를 반환한다.  
서브쿼리와 함께 사용될 때 IN과 ANY는 동일한 의미를 가지지만 IN은 목록을 받고, =ANY는 값 목록을 직접적으로 받아올 수 없다.
```
SELECT s1 FROM t1 WHERE s1 IN (1, 2, 3);  -- 가능
SELECT s1 FROM t1 WHERE s1 = ANY (1, 2, 3); -- 불가능
```
SOME 또한 ANY의 엘리아스이다.

 ## 15.2.15.4. Subqueries with ALL
```
피연산자 비교 연산자 ALL (서브쿼리)
```
ALL은 서브쿼리에서 반환된 모든 값에 대해 비교 연산자가 참이면 TRUE를 반환한다.  
즉, 하나라도 거짓일 시 FALSE를 반환하게 된다.
또한, 서브쿼리가 NULL 값을 포함하면, ALL의 연산값이 NULL이 되기도 한다.  
서브쿼리가 빈 테이블이라면 ALL 비교는 항상 TRUE가 된다.  
NOT IN은 <>ALL의 엘리아스이다, 하지만 NULL이 포함된 경우 차이가 발생할 수 있다.  
NOT IN은 앞서 서술했듯 서브쿼리 값에 NULL이 있으면 NULL이 결과값이 된다.
 
 ## 15.2.15.6. Subqueries with EXISTS or NOT EXISTS
 EXIST 
 - 서브쿼리가 하나 이상의 행을 반환하면 TRUE
 - 서브쿼리가 비어 있으면 FALSE
즉, 테이블에 행이 존재하는지 확인하는 기능을 한다.

NOT EXIST는 이 목록에 존재하지 않음 -> 다른 모든 곳에 존재함에서 활용가능하다.

 ## 15.2.15.10. Subquery Errors
오류 1. MYSQL에서는 서브쿼리 내에 LIMIT을 사용하여 IN, ALL, ANY, SOME으로 비교할 수 없다.  
 예시 코드는 다음과 같다.  
 ```
SELECT * FROM t1 WHERE s1 IN (SELECT s2 FROM t2 ORDER BY s1 LIMIT 1);
```
오류 2. 서브쿼리가 여러 개의 컬럼을 반환하면 오류가 발생한다. 
- 다중 칼럼을 반환하려면 ROW SUBQUERY를 사용한다.  
오류 3. 예상보다 많은 행을 반환하면 오류가 발생한다.
- ANY, IN, LIMIT 1을 활용한다.  
오류 4. 서브쿼리에서 업데이트나  삭제를 실행할 수 없다.
- WITH를 사용하여 해결한다.
- 
 ## 15.2.20 WITH (Common Table Expressions)
 CTE는 하나의 SQL문에서 여러 번 참조할 수 있느 임시테이블이다.  
 CTE를 정의하기 위해 WITH 절을 사용한다.  
 ```
WITH
  cte1 AS (SELECT a, b FROM table1),
  cte2 AS (SELECT c, d FROM table2)
SELECT b, d FROM cte1 JOIN cte2
WHERE cte1.a = cte2.c;

```

```
WITH [RECURSIVE]
    cte_name [(col_name [, col_name] ...)] AS (subquery)
    [, cte_name [(col_name [, col_name] ...)] AS (subquery)] ...

```
 
![Image](https://github.com/user-attachments/assets/fbbe666d-7385-4fe7-a369-236d9c448d8a)
```
1.
select * from tips
where total_bill >=
(select avg(total_bill) from tips)
```

![Image](https://github.com/user-attachments/assets/8694e65f-bf9c-49a8-974f-16fb7a4b8d3f)
```
2.
select * from tips
where day in 
(select day from tips
group by day
having sum(total_bill) >= 1500)
```
groupby와 having을 같이 사용한다는 것만 알면 쉬운 문제였다.
쉽게 하고 실수 없이 하는 법은 먼저
```
select day, sum(total_bill) from tips
group by day
```
로 요일을 확인하고 최종 결과가 이와 동일한지 확인하는 것이다.

<img src="https://github.com/user-attachments/assets/2bab8a3e-701b-4db7-abce-cf529269b88b" width="350">  

```
#3. 서브쿼리로 풀기
SELECT CATEGORY, PRICE AS MAX_PRICE, PRODUCT_NAME
FROM FOOD_PRODUCT
WHERE (CATEGORY, PRICE) IN (
    SELECT CATEGORY, MAX(PRICE) FROM FOOD_PRODUCT
    WHERE CATEGORY IN ('과자', '국', '김치', '식용유')
    GROUP BY CATEGORY)
ORDER BY MAX_PRICE DESC

#3. with로 풀기
WITH MAXP AS (
    SELECT CATEGORY, MAX(PRICE) AS MAX_PRICE FROM FOOD_PRODUCT
    WHERE CATEGORY IN ('과자', '국', '김치', '식용유')
    GROUP BY CATEGORY)
SELECT F.CATEGORY, F.PRICE AS MAX_PRICE, F.PRODUCT_NAME FROM FOOD_PRODUCT F
JOIN MAXP M
ON M.CATEGORY = F.CATEGORY AND M.MAX_PRICE = F.PRICE
ORDER BY F.PRICE DESC
```

주의해야 할 점은
```
SELECT CATEGORY, MAX(PRICE) AS MAX_PRICE, PRODUCT_NAME
FROM FOOD_PRODUCT
GROUP BY CATEGORY
HAVING CATEGORY IN ('과자','국','김치','식용유')
ORDER BY MAX_PRICE DESC
```
이렇게 한다면 결과는 출력되지만 PRODUCT_NAME을 제대로 출력하지 못하여 오답이 될 수 있다.

또한,
groupby having where의 사용에 대하여 알아두는 것이 좋다
groupby는 집계함수고
having과 where은 필터 역할을 한다.
이때 그룹화 전 필터링을 원한다면 where을, 그룹화 후 필터링을 하고 싶다면 having을 사용한다.
하지만 sum, avg 등의 집계 함수를 그룹화 전에 사용하고 싶다면, 서브쿼리나 with절을 사용해야 한다.
밑은 이에 대한 예시이다.

```
1. 서브쿼리
SELECT CATEGORY, SUM(TOTAL_SALES) AS TOTAL_REVENUE
FROM (
    SELECT * FROM SALES WHERE ORDER_DATE >= '2024-01-01'  -- 🔹 그룹화 전에 필터링
) AS filtered_sales
GROUP BY CATEGORY;

2. with(CTE)
WITH filtered_sales AS (
    SELECT * FROM SALES WHERE ORDER_DATE >= '2024-01-01'  -- 🔹 그룹화 전에 필터링
)
SELECT CATEGORY, SUM(TOTAL_SALES) AS TOTAL_REVENUE
FROM filtered_sales
GROUP BY CATEGORY;

```
