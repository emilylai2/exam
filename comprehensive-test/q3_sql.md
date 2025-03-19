
- 建立資料表

```sql
CREATE TABLE score (
    name VARCHAR(50) PRIMARY KEY,
    score INT
);
```
- 建立class表
```sql
CREATE TABLE class (
    name VARCHAR(50) PRIMARY KEY,
    class VARCHAR(10)
);
```
- 插入資料1
```
INSERT INTO score (name, score) VALUES
('John', 97),
('Mary', 100),
('David', 83),
('Sara', 89);
```
- 插入資料2
```
INSERT INTO class (name, class) VALUES
('John', 'A'),
('Mary', 'A'),
('David', 'C'),
('Sara', 'B');
```

- 執行查詢
```sql
select c.class
from class c
join score s on c.name = s.name
where s.score = (
    select distinct score
    from score
    order by score desc
    limit 1 offset 1
);
```

