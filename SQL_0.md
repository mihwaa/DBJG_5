![Image](https://github.com/user-attachments/assets/fbbe666d-7385-4fe7-a369-236d9c448d8a)
1.
```
select * from tips
where total_bill >=
(select avg(total_bill) from tips)
  ```

![Image](https://github.com/user-attachments/assets/8694e65f-bf9c-49a8-974f-16fb7a4b8d3f)
2.
```
select * from tips
where day in 
(select day from tips
group by day
having sum(total_bill) >= 1500)
```
