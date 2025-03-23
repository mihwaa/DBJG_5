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
14.20.1. Window Function Descriptions
14.20.4. Named Windows
14.19.1. Aggregate Function Descriptions
