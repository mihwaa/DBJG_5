## join
```
SELECT * FROM table1, table2;
SELECT * FROM table1 INNER JOIN table2 ON table1.id = table2.id;
SELECT * FROM table1 LEFT JOIN table2 ON table1.id = table2.id;
SELECT * FROM table1 LEFT JOIN table2 USING (id);
SELECT * FROM table1 LEFT JOIN table2 ON table1.id = table2.id
                       LEFT JOIN table3 ON table2.id = table3.id;
```
using은 두 테이블에 동일한 열이 있을 때 사용가능하다  
SELECT *일 때 USING은 공통 열을 하나로 병합해서 보여줌 (COALESCE 사용), ON은 양쪽 열을 모두 보여준다.  

```
SELECT * FROM t1, t2 JOIN t3 ON (t1.i1 = t3.i3);
```
join은 ,보다 우선순위가 높기 때문에 위 쿼리에서는 오류가 발생한다

## GROUP BY

MySQL은 GROUP BY 열에 함수적으로 종속되지 않은 열을 SELECT, HAVING, ORDER BY에서 참조하는 것을 허용한다.  
이 경우, 서버는 각 그룹에서 임의의 값을 선택할 수 있으며, 선택된 값은 비결정적일 수 있다.  
또한, 이 값은 ORDER BY 절로 제어할 수 없으며, 정렬은 값 선택 이후 수행되므로 정렬 기준이 되는 값이 무엇인지 보장할 수 없다.
```
SELECT name, address, MAX(age) FROM t GROUP BY name;
```
위 쿼리에서, address는 GROUP BY에 포함되지 않았고 집계 함수도 아니므로, name이 기본 키나 유일 NOT NULL 열이 아니라면 오류가 발생한다.  
-> 가끔 쿼리 실행은 되는데 답 틀리다고 나올 때 이런 경우가 많았음.  
```
SELECT name, ANY_VALUE(address), MAX(age) FROM t GROUP BY name;
```
name이 기본키라 address가 종속되거나, any_value를 활용하면 오류를 해결할 수 있다

중복 제거 후 정렬을 수행해야 하나, 어떤 행을 유지할지에 따라 c3 값이 달라질 수 있으므로, 이 쿼리는 잘못된 것으로 간주된다.  
따라서 ORDER BY 절의 표현식이 다음 조건 중 하나를 만족하지 않으면 쿼리는 허용되지 않는다:  

표현식이 SELECT 목록에 있는 항목과 동일함
표현식에 포함된 모든 열이 SELECT 목록에도 존재함

1번
```
SELECT 
    A.AUTHOR_ID, 
    A.AUTHOR_NAME, 
    B.CATEGORY, 
    SUM(S.SALES * B.PRICE) AS TOTAL_SALES
FROM BOOK_SALES S
JOIN BOOK B ON S.BOOK_ID = B.BOOK_ID
JOIN AUTHOR A ON B.AUTHOR_ID = A.AUTHOR_ID
WHERE S.SALES_DATE BETWEEN '2022-01-01' AND '2022-01-31'
GROUP BY A.AUTHOR_ID, A.AUTHOR_NAME, B.CATEGORY
ORDER BY A.AUTHOR_ID ASC, B.CATEGORY DESC;
```

맨 처음 오답
```
WITH TOT_SALES AS (SELECT SUM(SALES) AS TOTAL_SALES, B.BOOK_ID 
      FROM BOOK_SALES S JOIN BOOK B
      ON S.BOOK_ID = B.BOOK_ID
        WHERE SALES_DATE < '2022-02-01' AND SALES_DATE >= '2022-01-01'
        GROUP BY B.BOOK_ID) 
        
SELECT A.AUTHOR_ID, A.AUTHOR_NAME, B.CATEGORY, (T.TOTAL_SALES*B.PRICE) AS TOTAL_SALES
    FROM BOOK B
    JOIN AUTHOR A ON A.AUTHOR_ID = B.AUTHOR_ID
    JOIN TOT_SALES T ON T.BOOK_ID = B.BOOK_ID
ORDER BY A.AUTHOR_ID, B.CATEGORY DESC;
```
WITH 써서 해보겠다고 까불다가 코드는 돌아갔는데 답이 틀렸다
문제를 잘못 이해했다 (저자+카테고리별)이었어야 하는데, 내가 푼 건 그냥 책별 매출이다, 연산이 맞게 돌아가는지도 모르겠다


2번
```
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
```
비트연산자 활용법을 몰라서 답을 봤다...


