---
title: SQL) Subquery
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Database
tags:
    - DB
    - SQL
---
# ☑️ 문법
* `GROUP BY`절을 제외하고 다 사용가능하며 `WHERE`, `HAVING`절에서 제일 많이 활용된다.

```sql
SELECT select_list
FROM   table
WHERE  expr operator (SELECT select_list
                      FROM   table);
```

* 테이블에서 어떤 정보를 조회할 때 특정 범위 안에 있는 정보만 조회하고 싶은데 그 특정 범위도 알 수가 없어서 쿼리문으로 물어봐야 할 때 사용한다.<br>

## 예제

```sql
SELECT last_name, salary
FROM   employees
WHERE  salary > (SELECT salary
                 FROM   employees
                 WHERE  last_name = 'Abel');
```

* `Able`이라는 사람보다 높은 연봉을 가진 사람들을 조회하고 싶을 때<br><br>

```sql
SELECT last_name, job_id, salary
FROM   employees
WHERE  salary = (SELECT MIN(salary)
                 FROM   employees);
```

* 연봉이 가장 적은 사람의 이름, 부서, 연봉정보를 출력하고 싶을 때<br><br>

```sql
SELECT   department_id, MIN(salary)
FROM     employees
GROUP BY department_id
HAVING   MIN(salary) > (SELECT MIN(salary)
                        FROM   employees
                        WHERE  department_id = 50);
```

* 부서번호 50번의 가장 적은 연봉보다는 큰 부서별 가장 적은 연봉을 보고 싶을 때<br><br>

## Inline View
* `From`절에 `Subquery`가 작성된 경우

```sql
SELECT a.last_name, a.salary, a.department_id, b.salavg
FROM   employees a JOIN (SELECT   department_id, AVG(salary) salavg
                         FROM     employees
                         GROUP BY department_id) b -- 이 쿼리구문 안에서만 유효한 inline view
ON     a.department_id = b.department_id
WHERE  a.salary > b.salavg;
```

* `employees` 테이블에서 자기가 소속된 부서의 평균 급여보다 본인의 급여가 더 큰 사원만 출력할 때
* `Inline view`는 실제로 존재하는 테이블은 아닌 이 쿼리문을 위한 가상 테이블로, 실제 존재하는 테이블은 아니기 때문에 마지막에 한 칸 띄우고 앨리어스를 꼭 작성해야 한다.
