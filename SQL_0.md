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

#3. with로 풀기
```

