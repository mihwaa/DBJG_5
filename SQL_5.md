```
WITH SCORE AS (
    SELECT *, ROUND(AVG(REVIEW_SCORE),2) AS SCORE
    FROM REST_REVIEW
    GROUP BY REST_ID)

SELECT I.REST_ID, REST_NAME, FOOD_TYPE, FAVORITES, ADDRESS, SCORE
FROM SCORE S JOIN REST_INFO I
ON S.REST_ID = I.REST_ID
WHERE ADDRESS LIKE "서울%"
ORDER BY SCORE DESC , FAVORITES DESC
```
%가 제일 익숙해서 %를 활용하여 풀었는데
정규표현식의 형태가 많다는 것을 알게 되었다.


```
SELECT
    c.id,
    c.genotype,
    p.genotype AS parent_genotype
FROM
    ecoli_data c
    JOIN ecoli_data p
    ON c.parent_id = p.id
WHERE
    (c.genotype & p.genotype) = p.genotype
ORDER BY
    c.id
```
비트 연산자는 나올 때마다 헷갈린다..  
(c.genotype & p.genotype) = p.genotype  
이 부분이 & (비트연산자의 and)여서   
(c.genotype & p.genotype) 는 자식과 부모가 모두 가지고 있는 형질이 된다.  
따라서 (c.genotype & p.genotype) = p.genotype면 조건을 만족하게 된다.  
