## 1. GROUP_CONCAT 학습
   GROUP_CONCAT()는 GROUP BY로 묶인 각 그룹 내의 값을 하나의 문자열로 연결해주는 집계 함수

   예시
```
  SELECT GROUP_CONCAT(NAME ORDER BY NAME SEPARATOR '; ')
  FROM CART_PRODUCTS
  WHERE CART_ID = 448;

  SELECT student_name,
  GROUP_CONCAT(test_score)
  FROM student
  GROUP BY student_name;
```
distint나 separator를 지정할 수도 있다.  


🔗 programmers - 우유와 요거트가 담긴 장바구니
```
WITH CART_NAME_LIST AS (
    SELECT CART_ID, GROUP_CONCAT(NAME) AS PRODUCTS
    FROM CART_PRODUCTS
    GROUP BY CART_ID
)

SELECT CART_ID
    FROM CART_NAME_LIST
    WHERE PRODUCTS LIKE '%Milk%'
    AND PRODUCTS LIKE '%Yogurt%'
ORDER BY CART_ID;
```



🔗 programmers - 언어별 개발자 분류하기 
```
옛날에 했던 풀이

WITH CTE AS (
    SELECT
        CASE
            WHEN SKILL_CODE & (SELECT SUM(CODE) FROM SKILLCODES WHERE CATEGORY = 'Front End') > 0 
                AND SKILL_CODE & (SELECT SUM(CODE) FROM SKILLCODES WHERE NAME = 'Python') > 0
            THEN 'A'
            WHEN SKILL_CODE & (SELECT SUM(CODE) FROM SKILLCODES WHERE NAME = 'C#') > 0
            THEN 'B'
            WHEN SKILL_CODE & (SELECT SUM(CODE) FROM SKILLCODES WHERE CATEGORY = 'Front End') > 0
            THEN 'C'
            ELSE NULL
        END AS GRADE,
        ID, EMAIL
    FROM
        DEVELOPERS
    )

SELECT *
FROM CTE
WHERE GRADE IS NOT NULL
ORDER BY GRADE ASC, ID ASC;

----

with cte as (
    select ID, EMAIL, group_concat(NAME,'|', CATEGORY separator '|') as SKILL
    from DEVELOPERS
    inner join SKILLCODES
        on SKILL_CODE & CODE
    group by ID, EMAIL
)

select *
from (
    select 
        case when (SKILL like '%Front End%' and SKILL like '%Python%')
             then 'A'
             when (SKILL like '%C#%')
             then 'B'
             when (SKILL like '%Front End%')
             then 'C'
        end as GRADE
        , ID
        , EMAIL
    from cte
) as subquery
where GRADE is not NULL
order by GRADE, ID 
```
그룹 콘켓으로 못 풀겠어서 다른 코드를 참고했다.....


## 2. 재귀 Recursive Common Table

CTE 내부에서 자기 자신을 참조하는 서브쿼리를 포함하는 구조를 말한다.
예시
```
WITH RECURSIVE cte (n) AS (
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 5
)
SELECT * FROM cte;
```
(cte가 cte를 참조함)

재귀 CTE는 다음과 같은 두 부분으로 구성된다  

비재귀 부분 (초기 행을 반환)  
예: SELECT 1  

재귀 부분 (이전 결과 기반으로 추가 행 생성)  
예: SELECT n + 1 FROM cte WHERE n < 5  

재귀는 한번, from 절에서만 사용할 수 있다.
또한 집계함수, 윈도우 함수, 그룹바이, 오더바이, distinct는 재귀 select 안에서 금지된다.  


🔗 programmers - 입양 시각 구하기(2) 
```
WITH recursive hour_n (hour)
as(select 0
  union all
  select hour+1
  from hour_n where hour < 23)

select a.hour, count(b.animal_id) as count
from hour_n as a
left join animal_outs as b on a.hour = hour(b.datetime)
group by a.hour
order by a.hour
```
시각에 대한 부분을 재귀로 해야 한다는 건 알았는데 구현이 어려워 고민이 길어졌다...
결국 답을 찾아봐야만 했다.
