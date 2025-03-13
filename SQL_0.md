`0주차 내용 정리`  


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
