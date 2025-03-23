## 14.20.2. Window Function Concepts and Syntax  
윈도우 함수는 집계 함수가 하는 것처럼 여러 행에 대한 계산을 수행한다.  
집계함수와의 차이점은 각 행마다 결과를 반환한다는 것이다.  
이때 계산이 수행되는 행을 현재 행(current row)라고 하며, 해당 행과 관련된 대상 행 집합을 윈도우라고 한다.  
```
SELECT country, SUM(profit) AS country_profit
FROM sales
GROUP BY country
ORDER BY country;
이렇게 하면 국가별 이익의 총합이 국가별로 표기

SELECT
  year, country, product, profit,
  SUM(profit) OVER() AS total_profit,
  SUM(profit) OVER(PARTITION BY country) AS country_profit
FROM sales
ORDER BY country, year, product, profit;
이렇게 윈도우 함수를 사용하면 각 분류에 해당하는 국가_수익과 전체수익이 표기된다.
```  
OVER()절은 어떻게 행을 분할하고 정렬할지를 정의한다.  
OVER()가 비어있으면 전체 행을 하나의 그룹으로 간주한다. (TOTAL_PROFIT에서 비워두고 썼다.)  
이러한 윈도우 함수는 SELECT절과 ORDER BY 절에서만 사용 가능하다.  

```
OVER(WINDOW_SPEC)
여기에는 PARITION BY  (  ), ORDER BY expr [ASC/DESC]가 가능하고
FRAME절에는 PARTITION 안에서의 부분 범위를 지정할 수 있다.  

```

## 14.20.1. Window Function Descriptions
집계함수가 아닌 윈도우 함수에 대해 설명한다.
```
CUME_DIST()	누적 분포값 (현재 값 이하의 비율)
DENSE_RANK()	순위 (동점은 같은 순위, 순위 간 공백 없음)
FIRST_VALUE()	프레임의 첫 번째 행의 값 반환
LAG()	현재 행보다 이전 행의 값 반환
LAST_VALUE()	프레임의 마지막 행의 값 반환
LEAD()	현재 행보다 이후 행의 값 반환
NTH_VALUE()	프레임 내 N번째 행의 값 반환
NTILE(N)	N개의 그룹으로 나누고 각 행의 그룹 번호 반환
PERCENT_RANK()	백분위 순위 (현재 행의 상대적 위치)
RANK()	순위 (동점은 같은 순위, 다음 순위는 건너뜀)
ROW_NUMBER()	파티션 내 행 번호 (1부터 시작, 항상 고유)
```

## 14.20.4. Named Windows
WINDOW 절은 쿼리에서 HAVING 다음, ORDERBY 절 앞에 위치한다.
```
WINDOW window_name AS (window_spec)
       [, window_name AS (window_spec)] ...
```
여러 OVER 절에서 같은 윈도우 정의가 반복된다면, WINDOW절로 한번만 정의하고 참조한다.
```
SELECT
  val,
  ROW_NUMBER() OVER (ORDER BY val) AS row_number,
  RANK()       OVER (ORDER BY val) AS rank,
  DENSE_RANK() OVER (ORDER BY val) AS dense_rank
FROM numbers;

->

SELECT
  val,
  ROW_NUMBER() OVER w AS row_number,
  RANK()       OVER w AS rank,
  DENSE_RANK() OVER w AS dense_rank
FROM numbers
WINDOW w AS (ORDER BY val);
```
여기에서, 정의된 윈도우에 각각 추가 속성을 덧붙이는 것 또한 가능하다.
```
SELECT DISTINCT year, country,
  FIRST_VALUE(year) OVER (w ORDER BY year ASC)  AS first,
  FIRST_VALUE(year) OVER (w ORDER BY year DESC) AS last
FROM sales
WINDOW w AS (PARTITION BY country);
```
이미 붙여진 속성은 덧붙이면 안되며, 순환 참조는 불가능하다.

## 14.19.1. Aggregate Function Descriptions
```
AVG(expr)	평균 (※ DISTINCT와 함께는 OVER() 사용 불가)
BIT_AND(expr)	비트 AND
BIT_OR(expr)	비트 OR
BIT_XOR(expr)	비트 XOR
COUNT(expr)	NULL이 아닌 값 개수
COUNT(*)	전체 행 개수
JSON_ARRAYAGG(expr)	JSON 배열 생성
JSON_OBJECTAGG(key, val)	JSON 객체 생성
MAX(expr)	최댓값 (※ DISTINCT와 함께는 OVER() 사용 불가)
MIN(expr)	최솟값 (※ DISTINCT와 함께는 OVER() 사용 불가)
STD() / STDDEV() / STDDEV_POP()	모집단 표준편차
STDDEV_SAMP()	표본 표준편차
SUM(expr)	합계 (※ DISTINCT와 함께는 OVER() 사용 불가)
VAR_POP() / VARIANCE()	모집단 분산
VAR_SAMP()	표본 분산
```
위 기능들은 집계함수이지만 윈도우함수처럼 활용할 수 있다. 


## 문제풀이

1번
```
SELECT score,
DENSE_RANK() OVER(ORDER BY score DESC) AS 'rank'
FROM Scores
ORDER BY 'rank';
```
윈도우 함수 처음 써봤는데 하필 컬럼 이름이 rank여서 헷갈렸다.

2번
```
select * FROM(
  SELECT
    DATE(measured_at) AS today,
    DATE(LEAD(measured_at) OVER (ORDER BY DATE(measured_at))) AS next_day,
    pm10,
    LEAD(pm10) OVER (ORDER BY DATE(measured_at)) AS next_pm10
  FROM measurements) T  
where next_pm10 > pm10
```
레벨 2가 맞나? 어려웠다...
일단 서브쿼리 안에 있는 select절을 만드는 거 까지는 어떻게 했는데 그렇게 하니까 where절이 작동하지 않았다. where절을 사용하기 위해 서브쿼리 안에 넣어주었더니 성공했다.
