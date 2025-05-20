1번 - 온라인 쇼필몰의 월별 매출
```
WITH CANCEL AS (
SELECT 
  strftime('%Y-%m', order_date) as order_month, 
  SUM(price*quantity) as canceled_amount 
FROM orders O
  JOIN order_items I ON
  O.order_id = I.order_id
  WHERE O.order_id  LIKE 'C%'
  GROUP BY order_month),

NOTCAN AS (
  SELECT 
  strftime('%Y-%m', order_date) as order_month, 
  SUM(price*quantity) as ordered_amount
  FROM orders O
  JOIN order_items I ON
  O.order_id = I.order_id
  WHERE O.order_id NOT LIKE 'C%'
  GROUP BY order_month
)

SELECT C.order_month, ordered_amount, canceled_amount, ordered_amount+canceled_amount as total_amount
FROM CANCEL C JOIN NOTCAN N
ON C.order_month = N.order_month
ORDER BY C.order_month
```
재밌긴 한데 더 효율 좋은 방법이 있을 것 같다


2번 - 복수 국적 메달 수상자 찾기

```
SELECT DISTINCT a.name from records r
  join athletes a on a.id = r.athlete_id
  join teams t on t.id = r.team_id
  join games g on g.id = r.game_id
  where medal is not NULL AND year >= 2000
  group by athlete_id
  having count(DISTINCT team_id) > 1
  order by name
```




3번- 세명이 친구인 관계 찾기
```
SELECT 
	A.user_a_id, 
	A.user_b_id, 
	B.user_b_id as user_c_id
FROM 
	edges A 
		JOIN edges B 
			ON A.user_b_id = B.user_a_id
		JOIN edges C
			ON A.user_a_id = C.user_a_id AND B.user_b_id = C.user_b_id
WHERE A.user_a_id = 3820 OR
      A.user_b_id = 3820 OR
      user_c_id = 3820
```

문제가 어렵다기보다는.. 논리 구조를 머리에 그리고 그걸 구현하는 게 어려웠다
