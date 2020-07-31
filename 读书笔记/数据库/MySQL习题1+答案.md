```mysql
-- 借助于emp表和dept表
-- 注意：快捷键出现的函数如：`REPLACE`(str,from_str,to_str)，有``会引起错误
-- 1.找出各月倒数第3天受雇的所有员工.
SELECT
	ENAME,
	HIREDATE,
	LAST_DAY(HIREDATE) MOBTH_LAST
FROM
	EMP
WHERE
	HIREDATE = LAST_DAY(HIREDATE) - 2;

-- 2.找出早于39年前受雇的员工.
SELECT
	*
FROM
	EMP
WHERE
	TO_DAYS(HIREDATE) < TO_DAYS(
		date_format(now(), '%Y-%m-%d')
	) - 39 * 365;

-- 3.以首字母大写的方式显示所有员工的姓名.
SELECT
	CONCAT(
		UPPER(SUBSTR(ENAME, 1, 1)),
		LOWER(SUBSTR(ENAME FROM 2))
	)
FROM
	EMP;

-- 4.显示正好为5个字符的员工的姓名.
SELECT
	ENAME
FROM
	EMP
WHERE
	LENGTH(ENAME) = 5;

-- 5.显示不带有"R"的员工的姓名
CREATE TABLE TEMP LIKE EMP;

INSERT INTO TEMP SELECT
	*
FROM
	EMP;

DELETE
FROM
	TEMP
WHERE
	ENAME LIKE '%R%';

SELECT
	ENAME
FROM
	TEMP;

DROP TABLE TEMP;

-- 6.显示所有员工姓名的前三个字符.
SELECT
	SUBSTR(ENAME FROM 1 FOR 3)
FROM
	EMP;

-- 7.显示所有员工的姓名,用a替换所有"A"
SELECT
	REPLACE (ENAME, 'A', 'a')
FROM
	EMP;

-- 8.显示满10年服务年限的员工的姓名和受雇日期.:SELECT
SELECT
	ENAME,
	HIREDATE
FROM
	EMP
WHERE
	TO_DAYS(HIREDATE) < TO_DAYS(
		date_format(now(), '%Y-%m-%d')
	) - 10 * 365;

-- 9.显示员工的详细资料,按姓名排序.
SELECT
	*
FROM
	EMP
ORDER BY
	ENAME;

-- 10.显示员工的姓名和受雇日期,根据其服务年限,将最老的员工排在最前面.
SELECT
	ENAME,
	HIREDATE
FROM
	EMP
WHERE
	TO_DAYS(HIREDATE) < TO_DAYS(
		date_format(now(), '%Y-%m-%d')
	) - 10 * 365
ORDER BY
	HIREDATE;

-- 11.显示所有员工的姓名、工作和薪金,按工作的降序排序,若工作相同则按薪金排序.
SELECT
	ENAME,
	JOB,
	SAL
FROM
	EMP
ORDER BY
	JOB DESC,
	SAL ASC;

-- 12.显示所有员工的姓名、加入公司的年份和月份,按受雇日期所在月排序,若月份相同则将最早年份的员工排在最前面.
SELECT ENAME,YEAR(HIREDATE) '年份',MONTH(HIREDATE)'月份' FROM EMP ORDER BY
MONTH(HIREDATE),HIREDATE;
-- --13.取得每个部门最高薪水的人员名称
SELECT
	E.ENAME,
	T.*
FROM
	EMP E
JOIN (
	SELECT
		DEPTNO,
		MAX(SAL) AS MAXSAL
	FROM
		EMP
	GROUP BY
		DEPTNO
) T ON T.DEPTNO = E.DEPTNO
AND T.MAXSAL = E.SAL;

-- 14.找出在(任何年份的)2月受聘的所有员工。
SELECT
	*
FROM
	EMP
WHERE
	MONTH (HIREDATE) = 2;

-- 15.对于每个员工,显示其加入公司的天数.
SELECT
	ENAME,
	TO_DAYS(HIREDATE) AS 入职天数
FROM
	EMP;

-- 16.显示姓名字段的任何位置包含"A"的所有员工的姓名.
SELECT
	ENAME
FROM
	EMP
WHERE
	ENAME LIKE '%A%';

-- 17.列出所有job=‘CLERK’ 的员工平均薪资
SELECT
	ENAME,
	AVG(SAL)
FROM
	EMP
WHERE
	JOB = 'CLERK';

-- 18.列出job=‘CLERK’员工的平均薪资 按照部门分组 
SELECT
	DEPTNO,
	DNAME,
	AVG(SAL)
FROM
	(
		SELECT
			A.DEPTNO,
			DNAME,
			A.ENAME,
			SAL
		FROM
			(
				SELECT
					*
				FROM
					EMP
				WHERE
					JOB = 'CLERK'
			) A
		INNER JOIN DEPT B ON A.DEPTNO = B.DEPTNO
	) C
GROUP BY
	DNAME;

-- 19.列出job=‘CLERK’员工的平均薪资 按照部门分组 并且部门编号 in(10,30) 按照平均薪资 降序排列
SELECT
	DEPTNO,
	DNAME,
	AVG(SAL)
FROM
	(
		SELECT
			A.DEPTNO,
			DNAME,
			A.ENAME,
			SAL
		FROM
			(
				SELECT
					*
				FROM
					EMP
				WHERE
					JOB = 'CLERK'
			) A
		INNER JOIN DEPT B ON A.DEPTNO = B.DEPTNO
	) C
WHERE
	DEPTNO IN (10, 30)
GROUP BY
	DNAME
ORDER BY
	AVG(SAL) DESC;

-- 20.列出job=‘CLERK’员工的平均薪资 按照部门分组 并且部门编号 in(20,30) 并且部门员工数量>=2人 按照平均薪资 降序排列
SELECT
	*
FROM
	(
		SELECT
			DEPTNO,
			DNAME,
			AVG(SAL),
			COUNT(ENAME) AS CO
		FROM
			(
				SELECT
					A.DEPTNO,
					DNAME,
					A.ENAME,
					SAL
				FROM
					(
						SELECT
							*
						FROM
							EMP
						WHERE
							JOB = 'CLERK'
					) A
				INNER JOIN DEPT B ON A.DEPTNO = B.DEPTNO
			) C
		WHERE
			DEPTNO IN (20, 30)
		GROUP BY
			DNAME
		ORDER BY
			AVG(SAL) DESC
	) AS D
WHERE
	CO >= 2;
```

