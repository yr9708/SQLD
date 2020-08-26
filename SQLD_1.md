# SQLD_1

> Nologging

* DB에 데이터를 입력하면 로그파일에 그 정보를 기록

* Check point라는 이벤트가 발생하면 로그파일의 데이터를 데이터 파일에 저장한다.

* Nologging 옵션은 로그파일의 기록을 최소화 시켜서 입력 시 성능을 향상시키는 방법

* Buffer Cache 라는 메모리 영역을 생략하고 기록

  ```sql
  ALTER TABLE DEPT NOLOGGING;
  ```

  

>  TRUNCATE TABLE 테이블명

* 테이블의 모든 데이터를 삭제한다
* 데이터가 삭제되면 테이블의 용량은 초기화 된다 (DELETE TABLE은 테이블의 용량은 감소하지 않는다.)



> ORDER BY 

* SELECT 구문중 가장 늦게 실행된다.

* 정렬을 하기 때문에 데이터베이스 메모리를 많이 사용하게 된다.

  -> 정렬은 ORACLE DB에 부하를 주므로 ***INDEX***를 사용해서 ORDER BY 를 회피할 수 있다.

```sql
SELECT * FROM EMP ORDER BY EMPNO DESC;
SELECT /*+ INDEX_DESC(EMP) */* FROM EMP;
```

-> EMPNO가 기본키이고 생성된 순서대로 INDEX가 만들어지기 때문에 DESC 또는 ASC 할 수 있다.



> NVL, NVL2, NULLIF, COALESCE

```SQL
SELECT NVL(MGR, 0) FROM EMP;
```

-> NULL 이면 0 출력

```SQL
SELECT NVL2(MGR, 0, 1) FROM EMP;
```

-> NULL 아니면 0 NULL이면 1 출력

```sql
SELECT e.last_name, NULLIF(e.job_id, j.job_id) old_job_id
FROM employees e, job_history j
WHERE e.empolyee_id = j.employee_id
ORDER BY last_name;
```

-> last_name이 서로 같으면 null 값을 반환하고 같지 않으면 e.job_id를 출력

```SQL
SELECT COALESCE(COMM,1), COMM
FROM EMP;
```

-> comm이 null이면 1출력, 아니면 comm 출력



> STDDEV() : 표준편차를 계산
>
> * 분산 값의 제곱근

```sql
SELECT STDDEV(SAL) FROM EMP;
```

> VARIAN() : 분산을 계산
>
> * 분산? 주어진 범위의 개별 값과 평균값과의 차이인 편차를 구해 이를 제곱해서 평균한 값

```SQL
SELECT VARIANCE(SAL) FROM EMP;
```

실제로 통계에서는 평균을 중심으로 값들이 어느 정도 분포하는지를 나타내는 수치인 표준편차를 지표로 사용한다.



> COUNT(*) 
>
> * NULL을 포함한 모든 행을 계산

> COUNT(컬럼명)
>
> * NULL을 제외한 행을 계산



>  SELECT의 실행 순서

* FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY



> 형변환 함수

* TO_NUMBER(문자열)

```SQL
SELECT TO_NUMBER('200') FROM DUAL;
```

* TO_CHAR(숫자 혹은 날짜,[FORMAT])

```SQL
SELECT TO_CHAR(2020) FROM DUAL;
```

* TO_DATE(문자열,FORMAT)

```SQL
SELECT TO_DATE('20200825','yyyyMMdd') FROM DUAL;
```



> 숫자형 함수

* SIGN(숫자) : 양수,음수,0을 돌려준다 (양수면 1, 음수면 -1)

```SQL
SELECT SIGN(100) FROM DUAL;
```

-> 1 출력 됨

* MOD(숫자1, 숫자2) : 숫자1을 숫자2로 나누어 나머지를 계산

```SQL
SELECT MOD(8,3) FROM DUAL;
```

-> 2 출력 됨

* CEIL(숫자)/CEILING(숫자) : 숫자보나 크기가 크거나 같은 최소의 정수 출력

```SQL
SELECT CEIL(15.677) FROM DUAL;
```

-> 16 출력

* FLOOR(숫자) : 숫자보다 크기가 작거나 같은 최대의 정수 출력

```SQL
SELECT FLOOR(24.456) FROM DUAL;
```

-> 24 출력

* ROUND(숫자, M) : 소수점 M 자리에서 반올림

```SQL
SELECT ROUND(67.3334,3) FROM DUAL;
```

-> 67.333

* TRUNC(숫자,M) : 소수점 M 자리에서 절삭

```SQL
SELECT TRUNC(873.4422,3) FROM DUAL;
```

-> 873.442



> CASE문

```sql
SELECT SAL,CASE 
		WHEN SAL >=2500 THEN 'A'
		WHEN SAL >=1200 THEN 'B'
		ELSE 'C'
		END SAL_GRADE
FROM EMP;
```

-> 2500보다 같거나 크면 A 출력, 1200보다 같거나 크면 B 출력 그 이외엔 C 출력, AS로 SAL_GRADE 



> ROWNUM

* 화면에 데이터를 출력할 때 부여되는 논리적 순번이다. 만약 ROWNUM을 사용해서 페이지 단위 출력을 하기 위해서는 인라이 뷰(inline view -> sub query)를 사용해야 한다.

```SQL
SELECT * 
FROM (SELECT ROWNUM LIST, ENAME FROM EMP)
WHERE LIST BETWEEN 5 AND 10;
```

-> rownum의 as를 LIST 로 주고 그 LIST가 5~10일 때 ENAME 출력



> ROWID

* ORACLE DB 내에서 데이터를 구분할 수 있는 유일한 값
* 데이터가 어떤 데이터 파일, 어느 블록에 저장되어 있는지 알 수 있다.

```sql
SELECT ROWID, EMPNO FROM EMP;
```



> WITH
>
> * 서브쿼리를 사용해서 임시 테이블이나 뷰처럼 사용할 수 있는 구문
> * 옵티마이저는 SQL을 인라인 뷰나 임시 테이블로 판단한다

```SQL
WITH VIEWDATA AS 
	(SELECT * FROM EMP 
	UNION ALL
	SELECT * FROM EMP 
	)
SELECT * FROM VIEWDATA WHERE EMPNO = 7934;
```



> GRANT

```sql
GRANT privileges ON object TO user;
```

```SQL
GRANT SELECT, INSERT, UPDATE, DELETE
ON EMP
TO YR WITH GRANT OPTION;
```

-> YR에게 SELECT, INSERT, UPDATE, DELETE 할 수 있는 권한과 다른 사람에게 권한을 부여할 수 있는 권한을 부여한다.

```sql
GRANT SELECT, INSERT, UPDATE, DELETE
ON EMP
TO YR WITH ADMIN OPTION
```

-> 테이블에 대한 모든 권한을 부여한다.

