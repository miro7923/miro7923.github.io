---
title: GROUP BY절과 HAVING절
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
* 그룹함수와 함께 사용하며 조건에 맞는 그룹으로 묶어서 보여준다.

```sql
SELECT    column, group_function
FROM      table
[WHERE    contition]
[GROUP BY group_by_expression]
[HAVING   group_condition]
[ORDER BY column];
```

# 그룹함수
* `AVG` : 평균을 구해주는 함수
* `COUNT` : 개수를 세어주는 함수
* `MAX` : 최대값을 구해주는 함수
* `MIN` : 최소값을 구해주는 함수
    * 날짜와 문자의 최대값과 최소값도 구할 수 있는데 
    * 날짜에서의 최대값은 가장 최근 날짜, 최소값은 가장 과거 날짜
    * 문자에서는 알파벳/가나다 오름차순으로 보여준다.
* `STDDEV` : 표준편차를 구해주는 함수(쓸 일이 많지는 않다)
* `SUM` : 합계를 구해주는 함수
* `VARIANCE` : 분산을 구해주는 함수(쓸 일이 많지는 않다)

```sql
SELECT AVG(salary), MAX(salary),
       MIN(salary), SUM(salary)
FROM   employees
WHERE  job_id LIKE '%REP%';
```
```sql
SELECT COUNT(commission_pct)
FROM   employess
WHERE  department_id = 80;
```
<br><br>

# GROUP BY절
```sql
SELECT   department_id, AVG(salary)
FROM     employees
GROUP BY department_id;
```
```sql
SELECT   department_id, job_id, SUM(salary)
FROM     employees
WHERE    department_id > 40
GROUP BY department_id, job_id
ORDER BY department_id;
```
* `select`절에 있는 컬럼 리스트 중에서 그룹함수에 포함되어 있지 않은 컬럼은 반드시 `group by`절에 포함되어 있어야 문법 오류가 안 난다.<br><br>

# HAVING절
```sql
SELECT   job_id, SUM(salary) PAYROLL
FROM     employees
WHERE    job_id NOT LIKE '%REP%'
GROUP BY job_id
HAVING   SUM(salary) > 13000
ORDER BY SUM(salary);
```
* `GROUP BY`절과 `HAVING`절 사이에 순서는 없으나 `ORDER BY`절은 맨 마지막에 쓰는 것이 좋다.
* 왜냐면 최종 결과를 가지고 정렬하는 것이 가장 정확하니까.<br><br><br>


# JOIN
* 여러 테이블을 묶어서 데이터를 볼 때 사용한다. <br><br>

## ON절을 사용한 JOIN
```sql
SELECT e.employee_id, e.last_name, e.department_id,
       d.department_id, d.location_id
FROM   employees e JOIN departments d
ON     (e.department_id = d.department_id); -- 여기에 연결시킬 컬럼명을 입력한다.
```
```sql
SELECT e.employee_id, e.last_name, e.department_id,
       d.department_id, d.location_id
FROM   employees e JOIN departments d
ON     (e.department_id = d.department_id)
WHERE  e.manager_id = 149;
```
* 컬럼명을 입력할 때 해당 컬럼이 속해있는 테이블명을 입력하면 해당 범위에서만 검색을 시행하기 때문에 실행속도가 훨씬 빨라진다. (테이블명을 적지 않아도 실행되지만 그만큼 모든 테이블을 대상으로 검색해서 결과를 가져오기 때문에 실행속도가 훨씬 느리다.)
* 그래서 실행속도가 빠른 쿼리문을 작성하는 것이 중요하다.
* 그런데 테이블 풀네임을 일일이 적어주면 너무 길어서 가독성이 떨어지니까 약자로 적을 수 있는데 대신 약자로 적었다면 `FROM`절에서 어떤 테이블명의 약자인지 꼭 명시해줘야 `SQL`이 헷갈리지 않고 잘 찾아올 수 있다.
