## 14 - 5 다른 테이블과 관계를 맺는 FOREIGN KEY 
<br/>

- FOREIGN KEY는 서로 다른 테이블 간 관계를 정의하는 데 사용하는 제약 조건이다.
- 특정 테이블에서 PRIMARY KEY 제약 조건을 지정한 열을 다른 테이블의 특정 열에서 참조하겠다는 의미로 지정할 수 있다.

```sql
// EMP 테이블과 DEPT 테이블의 제약 조건 살펴보기
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, R_OWNER, R_CONSTRAINT_NAME
  FROM USER_CONSTRAINTS
 WHERE TABLE_NAME IN ('EMP', 'DEPT');
``` 

![](https://velog.velcdn.com/images/bebeco/post/c0301b6a-eab8-4840-9435-69d48d8ee55e/image.png)

▶︎ EMP 테이블의 DEPTNO 열은 다음과 같이 DEPT 테이블의 DEPTNO 열을 참조하여 저장 값의 범위를 정한다. 이렇게 참조 관계를 정의하면 EMP 테이블의 DEPTNO 열에는 DEPT 테이블의 DEPTNO 열에 존재하는 값과 NULL만 저장할 수 있게 된다.

<br/>

- EMP 테이블에 DEPTNO 열 값을 50으로 지정하고 INSERT문을 사용하면 오류가 나서 실행되지 않는다. 50은 DEPT 테이블의 DEPTNO 열에 저장되어 있지 않은 값이기 때문이다.

```sql
INSERT INTO EMP(EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
VALUES(9999, '홍길동', 'CLERK', '7788', TO_DATE('2017/04/30', 'YYYY/MM/DD'), 1200, NULL, 50);
```

![](https://velog.velcdn.com/images/bebeco/post/94766cf7-da03-477c-96d4-efc828d39fef/image.png)

<br/>

> FOREIGN KEY 지정하기

- 참조 대상을 지정하는 문법

```sql
// 기본 형식
CREATE TABLE 테이블 이름(
  ...(다른 열 정의),
  열 자료형 CONSTRAINT [제약 조건 이름] REFERENCES 참조 테이블(참조할 열)
);
```

```sql
// 제약 조건 이름을 지정하지 않고 FOREIGN KEY를 정의할 수 있다.
CREATE TABLE 테이블 이름(
  ...(다른 열 정의),
  열 자료형 REFERENCES 참조 테이블(참조할 열)
);
```

```sql
// 열을 모두 정의한 후 제약 조건을 지정하려면 마지막에 CONSTRAINT 키워드를 사용
CREATE TABLE 테이블 이름(
  ...(다른 열 정의),
  CONSTRAINT [제약 조건 이름] FOREIGN KEY(열)
  REFERENCES 참조 테이블(참조할 열)
);
```

```sql
// 참조 대상이 될 DEPT_FK 테이블 생성하기
CREATE TABLE DEPT_FK(
 DEPTNO NUMBER(2) CONSTRAINT DEPTFK_DEPTNO_PK PRIMARY KEY,
 DNAME  VARCHAR2(14),
 LOC    VARCHAR2(13)
); 
```

```sql
// DEPT_FK 테이블의 DEPTNO 열을 참조하는 FOREIGN KEY 제약 조건을 정의한 EMP_FK 테이블 생성하기
CREATE TABLE EMP_FK(
  EMPNO    NUMBER(4) CONSTRAINT EMPFK_EMPNO_PK PRIMARY KEY,
  ENAME    VARCHAR2(10),
  JOB      VARCHAR2(9),
  MGR      NUMBER(4),
  HIREDATE DATE,
  SAL      NUMBER(7,2),
  COMM     NUMBER(7,2),
  DEPTNO   NUMBER(2) CONSTRAINT EMPFK_DEPTNO_FK REFERENCES DEPT_FK (DEPTNO)
);
```

▶︎ EMP_FK 테이블의 DEPTNO 열은 이제 DEPT_FK 테이블의 DEPTNO 열을 참조하는 FOREIGN KEY 제약 조건이 지정되었다.

```sql
// DEPT_FK에 데이터 삽입하기
INSERT INTO DEPT_FK
VALUES(10, 'TEST_DNAME', 'TEST_LOC');
```

![](https://velog.velcdn.com/images/bebeco/post/21f124a0-d525-45c0-8d74-524b1b77b64e/image.png)

```sql
// EMP_FK 테이블에 데이터 삽입하기
INSERT INTO EMP_FK
VALUES(9999, 'TEST_NAME', 'TEST_JOB', NULL, TO_DATE('2001/01/01', 'YYYY/MM/DD'),
       3000, NULL, 10);
```

![](https://velog.velcdn.com/images/bebeco/post/00ac7e30-d36e-453f-b45e-5e738d0d00dd/image.png)


<br/>

> FOREIGN KEY로 참조 행 데이터 삭제하기


```sql
// DEPT_FK 테이블의 10번 부서 데이터 삭제하기
DELETE FROM DEPT_FK
WHERE DEPTNO = 10;
```

![](https://velog.velcdn.com/images/bebeco/post/df0d08d5-0426-43ed-b185-e90383faf4dd/image.png)

▶︎ 삭제하려는 DEPTNO 값을 참조하는 데이터가 존재하기 때문에 삭제하려면 다음 방법 중 한가지를 사용해야 한다.

1. 현재 삭제하려는 열 값을 참조하는 데이터를 먼저 삭제한다.
   ex) EMP_FK 테이블의 DEPTNO가 10번인 데이터를 삭제한 후 DEPT_FK 테이블의 10번 부서 삭제

2. 현재 삭제하려는 열 값을 참조하는 데이터를 수정한다.
   ex) EMP_FK 테이블의 DEPTNO가 10번인 데이터를 다른 부서 번호 또는 NULL로 변경한 후 DEPT_FK 테이블의 10번 부서 삭제
   
3. 현재 삭제하려는 열을 참조하는 자식 테이블의 FOREIGN KEY 제약 조건을 삭제한다.

⭐ 하지만 위 방법은 삭제할 데이터를 참조하는 데이터의 수정 또는 삭제 작업을 선행해야 하므로 다소 귀찮은 작업이 될 것이다.
따라서 제약 조건을 처음 지정할 때 다음과 같이 추가 옵션을 지정하는 방법을 사용하기도 한다. 이 방법은 데이터 삭제와 더불어 삭제할 데이터를 참조하는 처리를 어떻게 할지 정할 수 있다.

<br/>

### 열 데이터를 삭제할 때 이 데이터를 참조하고 있는 데이터도 함께 삭제

```sql
// 기본 형식
CONSTRAINT [제약 조건 이름] REFERENCES 참조 테이블(참조할 열) ON DELETE CASCADE
```

▶︎ DEPT_FK 테이블의 DEPTNO 열 값이 10인 데이터를 삭제하면 이를 참조하는 EMP_FK 테이블의 DEPTNO 열 값이 10인 데이터도 함께 삭제한다.

![](https://velog.velcdn.com/images/bebeco/post/3a3fcce5-791d-4f66-89ff-2503c3efaee2/image.png)

![](https://velog.velcdn.com/images/bebeco/post/4ac0065e-e69e-4202-ac8f-8b067d7cb69a/image.png)

<br/>

### 열 데이터를 삭제할 때 이 데이터를 참조하는 데이터를 NULL로 수정

```sql
// 기본 형식
CONSTRAINT [제약 조건 이름] REFERENCES 참조 테이블(참조할 열) ON DELETE SET NULL
```

▶︎ DEPT_FK 테이블의 DEPTNO 열 값이 10인 데이터를 삭제하면 이를 참조하는 EMP_FK 테이블의 DEPTNO 열 값이 10인 데이터를 NULL로 수정한다.

<br/>

## 14 - 6 데이터 형태와 범위를 정하는 CHECK

<br/>

- CHECK 제약 조건은 열에 저장할 수 있는 값의 범위 또는 패턴을 정의할 때 사용한다.

```sql
// 테이블을 생성할 때 CHECK 제약 조건 설정하기
CREATE TABLE TABLE_CHECK(
    LOGIN_ID  VARCHAR2(20) CONSTRAINT TBLCK_LOGINID_PK PRIMARY KEY,
    LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLCK_LOGINPW_CK CHECK (LENGTH(LOGIN_PWD) > 3),
    TEL       VARCHAR2(20)
);

```

▶︎ CHECK 키워드 다음의 LENGTH(LOGIN_PWD) > 3은 LOGIN_PWD 열 길이가 3이상인 데이터만 저장 가능하다는 뜻이다. 

<br/>

```sql
// CHECK 제약 조건에 맞는 예
INSERT INTO TABLE_CHECK
VALUES ('TEST_ID', '1234', '010-1234-5678');
```

![](https://velog.velcdn.com/images/bebeco/post/c8e23188-fcaf-4f5e-9660-15e16acdc809/image.png)

<br/>

## 14 - 7 기본값을 정하는 DEFAULT

<br/>

- 제약 조건과는 별개로 특정 열에 저장할 값이 지정되지 않았을 경우에 기본값(default)을 지정할 수 있다.

```sql
// 테이블을 생성할 때 DEFAULT 제약 조건 설정하기
CREATE TABLE TABLE_DEFAULT(
    LOGIN_ID  VARCHAR2(20) CONSTRAINT TBLCK2_LOGINID_PK PRIMARY KEY,
    LOGIN_PWD VARCHAR2(20) DEFAULT '1234',
    TEL       VARCHAR2(20)
);
```

![](https://velog.velcdn.com/images/bebeco/post/f6ac7457-32d9-422a-9f1b-a1b51560a11c/image.png)

```sql
// DEFAULT로 지정한 기본값이 입력되는 INSERT문 확인하기
INSERT INTO TABLE_DEFAULT VALUES ('TEST_ID', NULL, '010-1234-5678');

INSERT INTO TABLE_DEFAULT (LOGIN_ID, TEL) VALUES ('TEST_ID2', '010-1234-5678');
```

![](https://velog.velcdn.com/images/bebeco/post/50641759-9cd4-43d9-9873-fef8fb68bc1d/image.png)


<br/>

## 잊기 전에 한 번 더!

Q1.

①
```sql
CREATE TABLE DEPT_CONST (
DEPTNO NUMBER(2) CONSTRAINT DEPTCONST_DEPTNO_PK PRIMARY KEY,
DNAME VARCHAR2(14) CONSTRAINT DEPTCONST_DNAME_UNQ UNIQUE,
LOC VARCHAR2(13) CONSTRAINT DEPTCONST_LOC_NN NOT NULL
);
```

②
```sql
CREATE TABLE EMP_CONST (
EMPNO NUMBER(4) CONSTRAINT EMPCONST_EMPNO_PK PRIMARY KEY,
ENAME VARCHAR2(10) CONSTRAINT EMPCONST_ENAME_NN NOT NULL,
JOB VARCHAR2(9),
TEL VARCHAR2(20) CONSTRAINT EMPCONST_TEL_UNQ UNIQUE,
HIREDATE DATE,
SAL NUMBER(7,2) CONSTRAINT EMPCONST_SAL_CHK CHECK (SAL BETWEEN 1000 AND 9999),
COMM NUMBER(7,2),
DEPTNO NUMBER(2) CONSTRAINT EMPCONST_DEPTNO_FK REFERENCES DEPT_CONST (DEPTNO)
);
```

③
```sql
SELECT TABLE_NAME, CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME IN ('DEPT_CONST', 'EMP_CONST');
```

---
