1번
```
select 
 CASE 
    WHEN A+B <= C OR B+C <= A OR A+C <= B THEN 'Not A Triangle'
    WHEN A=B AND B=C AND C=A THEN 'Equilateral'
    WHEN A<>B AND B<>C AND C<>A THEN 'Scalene'
    WHEN A=B AND A<>C THEN 'Isosceles'
    WHEN B=C AND A<>B THEN 'Isosceles'
    WHEN A=C AND A<>B THEN 'Isosceles' END AS TYPE from TRIANGLES

CASE문에서 앞에 건 조건부터 걸려서, NOT A TRIANGLE을 마지막에 두니까 제대로 정답이 출력이 안 됐다.
```

2번
```
SELECT NAME FROM Customer
WHERE referee_id != 2 or referee_id is null;

null은 !=2같은 연산자 계산할 때 항상 조심해야 한다.
```
