# SQL面试50题

# 表结构

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled.png)

### 1.查询课程编号为“01”的课程比“02”的课程成绩高的所有学生的学号

```sql
select a.s_id "s_no", s_name "s_name",a.s_score "01", b.s_score "02" from 
(
	select s_id,c_id,s_score from Score where c_id = '01'
) as a
inner join
(
	select s_id,c_id,s_score from Score where c_id = '02'
) as b on a.s_id=b.s_id
INNER JOIN Student as c on c.s_id = a.s_id
where a.s_score > b.s_score
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%201.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%201.png)

### 2.查询平均成绩大于60分的学生的学号和平均成绩

```sql
select s_id,avg(s_score) from score group by s_id having avg(s_score)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%202.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%202.png)

### 3. 查询所有学生的学号、姓名、选课数、总成绩

```sql
select a.s_id,a.s_name, count(b.c_id), 
sum(CASE when b.s_score is null then 0 else b.s_score END)  
from Student as a left join Score as b on a.s_id = b.s_id
group by a.s_id
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%203.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%203.png)

### 4. 查询姓“猴”的老师的个数

```sql
select count(distinct t_id) from Teacher where t_name like "侯%"
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%204.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%204.png)

### 5. 查询没学过“张三”老师课的学生的学号、姓名

```sql
method 1:
select s_id,s_name from Student where s_id not in(
	select s_id from Score 
		where c_id = (
			select c_id from Course where t_id = (
				select t_id from Teacher where t_name = '张三'
			)
	)
)

method 2:
select s_id,s_name from Student where s_id not in(
	select s_id from Score as a inner join Course as b
	on a.c_id = b.c_id 
		inner join Teacher as c
		on b.t_id = c.t_id  where t_name = "张三"
)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%205.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%205.png)

### 6. 查询学过“张三”老师所教的所有课的同学的学号、姓名

```sql
select s_id, s_name from Student where s_id in (
	select s_id from Score where c_id = (
		select c_id from Course where t_id = (
			select t_id from Teacher where t_name = "张三"
		)
	)
)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%206.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%206.png)

### 7. 查询学过编号为“01”的课程并且也学过编号为“02”的课程的学生的学号、姓名

```sql
select s_id,s_name from Student where s_id in (
select a.s_id from  (
	(select s_id,c_id from Score where c_id = "01" ) as a 
    inner join 
    (select s_id,c_id from Score where c_id = "02" ) as b
	on a.s_id = b.s_id 
) where a.c_id = "01" and b.c_id = "02" )
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%207.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%207.png)

### 8.查询课程编号为“02”的总成绩

```sql
select sum(s_score),avg(s_score),count(distinct s_id) from Score where c_id = "02"
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%208.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%208.png)

### 9.查询所有课程成绩小于60分的学生的学号、姓名

```sql
select s_id,s_name from Student where s_id in (SELECT a.s_id  from 
(SELECT s_id,count(c_id) as cnt from Score where s_score<60 group by s_id) as a
inner join
(select s_id, count(c_id) as cnt from Score group by s_id) as b
on a.s_id = b.s_id  where a.cnt = b.cnt
)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%209.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%209.png)

### 10. 查询没有学全所有课的学生的学号、姓名

```sql
select st.* from Student as st left join Score 
as sc on st.s_id = sc.s_id group by st.s_id 
having count(distinct sc.c_id) < (select count(c_id) from Course)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2010.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2010.png)

### 11. 查询至少有一门课与学号为“01”的学生所学课程相同的学生的学号和姓名

```sql
select s_id,s_name from Student where s_id in (
	select distinct(s_id) from Score where c_id in (
		select c_id from Score where s_id = "01"
	) and s_id != "01"
)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2011.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2011.png)

### 12.查询和“01”号同学所学课程完全相同的其他同学的学号

```sql
method 1:

select s_id from Score where s_id in (
select s_id from Score where c_id in (
	select c_id from Score where s_id = "01"
) and s_id != "01" group by s_id having count(distinct c_id) 
= (select count(distinct c_id) from Score where s_id = "01")
) group by s_id having count(distinct c_id) 
= (select count(distinct c_id) from Score where s_id = "01")

method 2:

select * from Student where s_id in (
	select s_id from Score where s_id!="01" group by s_id having 
	count(distinct c_id) = (select count(distinct c_id) from Score where s_id = "01")

	and s_id not in (
	
		select s_id from Score where c_id not in(
			select c_id from Score where s_id = "01"
		)

	)
)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2012.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2012.png)

### 13. 查询没学过"张三"老师讲授的任一门课程的学生姓名

```sql
select * from Student where s_id not in(
	select s_id from Score as sc 
	inner join Course as co on sc.c_id = co.c_id
	inner join Teacher as te on co.t_id = te.t_id where t_name = "张三"
)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2013.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2013.png)

### 15. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

```sql
select st.s_id,st.s_name, avg(sc.s_score) from Student as st inner join Score as sc on st.s_id = sc.s_id
where st.s_id in(
	select s_id from score where s_score<60 group by s_id having count(distinct c_id) >=2
)
group by st.s_id,st.s_name
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2014.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2014.png)

### 16. 检索"01"课程分数小于60，按分数降序排列的学生信息

```sql
select * from Student as st inner join Score sc 
on st.s_id = sc.s_id where sc.c_id = "01"
and sc.s_score < 60 order by sc.s_score desc
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2015.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2015.png)

### 17. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```sql
method 1:
select *,avg(s_score) over(partition by s_id) 
as average from Score order by average desc

method 2:

select s_id "学号",
max(case when c_id='01' then s_score else null end) "语文",
max(case when c_id='02' then s_score else null end) "数学",
max(case when c_id='03' then s_score else null end) "英语",
avg(s_score) "平均成绩"
from Score group by s_id order by "平均成绩" desc
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2016.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2016.png)

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2017.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2017.png)

### 18. 查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率

```sql
SELECT a.c_id,a.c_name,max(b.s_score), min(b.s_score), avg(b.s_score),
sum(case when b.s_score >= 60 then 1 else 0 END)/count(b.s_id) "及格",
sum(case when b.s_score >= 70 and b.s_score <=80 then 1 else 0 END)/count(b.s_id) "中等",
sum(case when b.s_score >= 80 and b.s_score <=90 then 1 else 0 END)/count(b.s_id) "优良",
sum(case when b.s_score >= 90 then 1 else 0 END)/count(b.s_id) "优秀"
from 
(SELECT * from Course ) as a INNER JOIN 
(SELECT * from Score) as b on a.c_id = b.c_id GROUP BY a.c_id
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2018.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2018.png)

### 19. 按各科成绩进行排序，并显示排名

```sql
SELECT *,ROW_NUMBER() over(PARTITION by c_id
 order by s_score ) as ranking from Score
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2019.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2019.png)

### 20. 查询学生的总成绩并进行排名

```sql
SELECT s_id,sum(s_score) as total 
from Score GROUP BY s_id ORDER BY total desc
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2020.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2020.png)

### 21. 查询不同老师所教不同课程平均分从高到低显示

```sql
select b.t_id,b.c_id,avg(a.s_score) from Score as a
inner JOIN Course as b on a.c_id = b.c_id 
GROUP BY b.t_id,b.c_id order by "平均分" DESC
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2021.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2021.png)

### 22. 查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

```sql
SELECT * FROM(
select sc.s_id,sc.c_id,sc.s_score,st.s_name,st.s_birth,ROW_NUMBER() 
over(Partition by c_id order by s_score desc) m from Score as sc INNER JOIN
Student as st on st.s_id = sc.s_id ) a where m in (2,3)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2022.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2022.png)

### 23.使用分段[100-85],[85-70],[70-60],[<60]来统计各科成绩，分别统计各分数段人数：课程ID和课程名称

```sql
SELECT sc.c_id,co.c_name, 

avg(case when sc.s_score>=85 and sc.s_score<=100 then 1 else 0 end) "85-100",
count(case when sc.s_score>=85 and sc.s_score<=100 then 1 else null end) "85-100个数",

avg(case when sc.s_score>=70 and sc.s_score<=85 then 1 else 0 end) "70-85",
count(case when sc.s_score>=70 and sc.s_score<=85 then 1 else null end) "70-85个数",

avg(case when sc.s_score>=60 and sc.s_score<=70 then 1 else 0 end) "60-70",
count(case when sc.s_score>=60 and sc.s_score<70 then 1 else null end) "60-70个数",

avg(case when sc.s_score<60 then 1 else 0 END) "60以下",
count(case when sc.s_score<60 then 1 else null end) "60以下个数"

from Score sc 
INNER JOIN Course co 
on sc.c_id = co.c_id GROUP BY sc.c_id, co.c_name
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2023.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2023.png)

### 24. 查询学生平均成绩及其名次

```sql
SELECT  st.s_id, avg(sc.s_score), ROW_NUMBER() 
over( ORDER BY avg(sc.s_score) DESC)
from Score sc 
INNER JOIN Student st on st.s_id = sc.s_id  GROUP BY st.s_id
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2024.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2024.png)

### 25. 查询各科成绩前三名的记录（不考虑成绩并列情况）

```sql
SELECT * from (
SELECT st.s_id,st.s_name,sc.c_id,sc.s_score, ROW_NUMBER() 
over( PARTITION by c_id ORDER BY s_score DESC) 
m FROM Score sc INNER JOIN Student st 
on st.s_id = sc.s_id ORDER BY sc.c_id ) as a WHERE m in (1,2,3)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2025.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2025.png)

### 26. 查询每门课程被选修的学生数

```sql

SELECT Score.c_id,Course.c_name, count(DISTINCT s_id)
from Score INNER JOIN Course 
on Score.c_id = Course.c_id GROUP BY Score.c_id, Course.c_name
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2026.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2026.png)

### 27. 查询出只有两门课程的全部学生的学号和姓名

```sql
method: 1

SELECT * from Student WHERE s_id in (
SELECT s_id FROM Score
group by s_id  having count(Score.c_id) =2 )

method: 2
SELECT st.s_id,st.s_name,count(sc.s_score) from Student st 
INNER JOIN Score sc on st.s_id = sc.s_id 
GROUP BY st.s_id,st.s_name  HAVING count(sc.s_score) = 2
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2027.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2027.png)

### 28. 查询男生、女生人数

```sql
SELECT s_sex,count(s_sex) from Student GROUP BY s_sex
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2028.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2028.png)

### 29. 查询名字中含有"风"字的学生信息

```sql
SELECT * FROM Student WHERE s_name like '%风%'
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2029.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2029.png)

### 31. 查询1990年出生的学生名单

```sql
SELECT * from Student WHERE YEAR(s_birth) = '1990'
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2030.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2030.png)

### 32. 查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩

```sql
SELECT sc.s_id,st.s_name,avg(s_score) FROM Score sc INNER JOIN Student st 
on sc. s_id = st.s_id   GROUP BY sc.s_id,st.s_name HAVING avg(s_score)>=85
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2031.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2031.png)

### 33. 查询每门课程的平均成绩，结果按平均成绩升序排序，平均成绩相同时，按课程号降序排列

```sql
SELECT c_id, avg(s_score) FROM Score GROUP BY c_id 
ORDER BY avg(s_score) asc, c_id desc
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2032.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2032.png)

### 34.查询课程名称为"数学"，且分数低于60的学生姓名和分数

```sql
SELECT sc.s_id,st.s_name,co.c_name,s_score from Score sc 
INNER JOIN Course co on sc.c_id = co.c_id 
INNER JOIN Student st on sc.s_id = st.s_id 
WHERE co.c_name = "数学" and s_score < 60
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2033.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2033.png)

### 35. 查询所有学生的课程及分数情况

```sql
SELECT sc.s_id,st.s_name,
MAX(CASE WHEN co.c_name = "语文" then s_score else null END) "语文",
MAX(CASE WHEN co.c_name = "数学" then s_score else null END) "数学",
MAX(CASE WHEN co.c_name = "英语" then s_score else null END) "英语",
MAX(CASE WHEN co.c_name = "化学" then s_score else null END) "化学"
from Score sc 
INNER JOIN Course co on sc.c_id = co.c_id 
INNER JOIN Student st on sc.s_id = st.s_id 
GROUP BY sc.s_id,st.s_name
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2034.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2034.png)

### 36. 查询任何一门课程成绩在70分以上的姓名、课程名称和分数

```sql
SELECT sc.s_id,st.s_name,sc.s_score,co.c_name FROM Score sc 
INNER JOIN Student st on sc.s_id = st.s_id
INNER JOIN Course co on sc.c_id = co.c_id
WHERE sc.s_score > 70
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2035.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2035.png)

### 37. 查询不及格的课程并按课程号从大到小排列

```sql
SELECT sc.*,co.c_name, co.t_id FROM Score sc INNER JOIN Course co
on sc.c_id = co.c_id WHERE s_score < 60 
ORDER BY co.c_id DESC
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2036.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2036.png)

### 38. 查询课程编号为03且课程成绩在80分以上的学生的学号和姓名

```sql
SELECT sc.s_id,sc.s_score, st.s_name, co.c_name from Score sc 
INNER JOIN Student st on sc.s_id = st.s_id
INNER JOIN Course co on sc.c_id = co.c_id
WHERE sc.c_id = '03' and s_score > 80
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2037.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2037.png)

### 39. 求每门课程的学生人数

```sql
SELECT sc.c_id, co.c_name, COUNT(DISTINCT s_id) FROM Score sc
INNER JOIN Course co on sc.c_id = co.c_id
GROUP BY sc.c_id
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2038.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2038.png)

### 40. 查询选修“张三”老师所授课程的学生中成绩最高的学生姓名及其成绩

```sql
SELECT s_id,t_name,s_score FROM Score s 
INNER JOIN Course c on s.c_id = c.c_id 
INNER JOIN Teacher te on c.t_id = te.t_id
WHERE te.t_name = "张三"  ORDER BY s.s_score DESC LIMIT 0,1
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2039.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2039.png)

### 41. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

```sql
SELECT a.s_id FROM 
(SELECT s_id FROM Score GROUP BY s_id 
HAVING COUNT(DISTINCT c_id) > 1) as a 
INNER JOIN 
(SELECT s_id,s_score  FROM Score GROUP BY s_id,s_score )  
as b on a.s_id = b.s_id
GROUP BY a.s_id HAVING MAX(s_score) = MIN(s_score)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2040.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2040.png)

### 42. 查询每门功成绩最好的前两名

```sql
SELECT * FROM (
select sc.*,co.c_name,st.s_name,ROW_NUMBER() over(PARTITION by c_id ORDER BY s_score DESC) m from Score sc 
INNER JOIN Student st on sc.s_id = st.s_id
INNER JOIN Course co on co.c_id = sc.c_id 
) a 
WHERE m in (1,2)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2041.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2041.png)

### 43. 统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

```sql
SELECT c_id,count(DISTINCT s_id) alias FROM Score GROUP BY c_id 
HAVING alias > 5
ORDER BY alias DESC,c_id ASC
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2042.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2042.png)

### 44. 检索至少选修两门课程的学生学号

```sql
SELECT s_id,COUNT(c_id) coursenum from Score
 GROUP BY s_id HAVING coursenum >= 2
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2043.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2043.png)

### 45. 查询选修了全部课程的学生信息

```sql
SELECT sc.s_id,COUNT(DISTINCT c_id) coursenum from Score sc 
INNER JOIN Student st on sc.s_id = st.s_id 
GROUP BY sc.s_id HAVING  coursenum  = (SELECT count(*) FROM Course)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2044.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2044.png)

### 46. 查询各学生的年龄

```sql
SELECT *,FLOOR(DATEDIFF(now(),s_birth)/365) from Student
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2045.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2045.png)

### 47. 查询没学过“张三”老师讲授的任一门课程的学生姓名

```sql
SELECT * from Student WHERE s_id not in(
SELECT s_id FROM Score sc 
INNER JOIN Course co on sc.c_id = co.c_id 
INNER JOIN Teacher te on te.t_id = co.t_id
WHERE t_name = "张三")
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2046.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2046.png)

### 48. 查询下周过生日的学生

```sql
SELECT * FROM Student WHERE 
WEEK(CONCAT('2020-',SUBSTRING(s_birth,6,5)),1)
= WEEK('2021-02-11',1) + 1
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2047.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2047.png)

### 49. 查询本月过生日的人

```sql
SELECT * from Student WHERE MONTH(s_birth) =MONTH(now())
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2048.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2048.png)

### 50. 查询下月过生日的人

```sql
SELECT * FROM Student WHERE MOD(MONTH(NOW())+ 1,12) =
 MONTH(s_birth)
```

![SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2049.png](SQL%E9%9D%A2%E8%AF%9550%E9%A2%98%200dbdb088ae97482d8531f38da98bd269/Untitled%2049.png)