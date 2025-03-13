
1.
```
select * from tips
where total_bill >=
(select avg(total_bill) from tips)
  ```

2.
```
select * from tips
where day in 
(select day from tips
group by day
having sum(total_bill) >= 1500)
```
