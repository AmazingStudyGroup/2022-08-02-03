## 14 - 1 제약 조건 종류
<br/>

> 제약 조건이란?

- 오라클에서 사용하는 제약 조건은 테이블의 특정 열에 지정한다. 제약 조건을 지정한 열에 제약 조건에 부합하지 않는 데이터를 저장할 수 없다. 제약 조건 지정 방식에 따라 기존 데이터의 수정이나 삭제 가능 여부도 영향을 받는다.

▶︎ 예를 들어 회원 가입할 때 이름, 생년월일 등의 데이터는 필수 입력 항목으로 두어 빈값(NULL)을 허용하지 않도록 지정할 수 있다.

- 오라클 데이터베이스에서 사용하는 제약 조건을 다음과 같다.

| 종류 | 설명 |
|:------:|:------------------:|
| NOT NULL | 지정한 열에 NULL을 허용하지 않습니다. NULL을 제외한 데이터의 중복은 허용됩니다. |
| UNIQUE | 지정한 열이 유일한 값을 가져야 합니다. 즉 중복될 수 없습니다. 단 NULL은 값의 중복에서 제외됩니다. |
| PRIMARY KEY | 지정한 열이 유일한 값이면서 NULL을 허용하지 않습니다. PRIMARY KEY는 테이블에 하나만 지정 가능합니다. |
| FOREIGN KEY | 다른 테이블의 열을 참조하여 존재하는 값만 입력할 수 있습니다. |
| CHECK | 설정한 조건식을 만족하는 데이터만 입력 가능합니다. |
<br/>


> 데이터 무결성이란?

- 데이터 무결성(data integrity)은 데이터베이스에 저장되는 데이터의 정확성과 일관성을 보장한다는 의미이며 이를 위해 항상 유지해야 하는 기본 규칙을 가지고 있다. 제약 조건은 이러한 데이터 무결성을 지키기 위한 안전장치로서 중요한 역할을 한다.

| 종류 | 설명 |
|:--------:|:-----------------:|
| 영역 무결성<br/> (domain integrity) | 열에 저장되는 값의 적정 여부를 확인. 자료형, 적절한 형식의 데이터, NULL 여부같은 정해 놓은 범위를 만족하는 데이터임을 규정 |
| 개체 무결성<br/> (entity integrity) | 테이블 데이터를 유일하게 식별할 수 있는 기본키는 반드시 값을 가지고 있어야 하며 NULL이 될 수 없고 중복될 수도 없음을 규정 |
| 참조 무결성<br/> (referential integrity) | 참조 테이블의 외래키 값은 참조 테이블의 기본키로서 존재해야 하며 NULL이 가능 |

▶︎ 이러한 무결성을 보장하기 위해 오라클에서는 앞에서 살펴본 다섯 가지 제약 조건을 제공한다.
제약 조건은 데이터베이스 설계 시점에 주로 지정한다. 하지만 테이블 생성 후에도 추가•변경•삭제할 수 있다. 따라서 제약 조건은 데이터 정의어(DDL)에서 활용한다.
<br/>

## 14 - 2 빈값을 허락하지 않는 NOT NULL
<br/>

> 테이블을 생성하며 제약 조건 지정

- NOT NULL은 특정 열에 데이터의 중복 여부와는 상관없이 NULL의 저장을 허용하지 않는 제약 조건이다. <span style="color: red">반드시열에 값이 존재해야만 하는 경우에 지정한다.</span>
```sql
// 테이블을 생성할 때 NOT NULL 설정하기
CREATE TABLE TABLE_NOTNULL(
    LOGIN_ID  VARCHAR2(20) NOT NULL,
    LOGIN_PWD VARCHAR2(20) NOT NULL,
    TEL       VARCHAR(20)
);
```

![](https://velog.velcdn.com/images/bebeco/post/767e6e10-15eb-46c6-97da-b544169ba9b8/image.png)

---

```sql
// 제약 조건이 NOT NULL인 열에 NULL 값 넣어보기
INSERT INTO TABLE_NOTNULL (LOGIN_ID, LOGIN_PWD, TEL)
VALUES ('TEST_ID_01', NULL, '010-1234-5678');
```

![](https://velog.velcdn.com/images/bebeco/post/15fc73ef-dd1e-4b22-a215-c629a9dc5ca5/image.png)

---

```sql
// 제약 조건이 없는 TEL 열에 NULL 값 입력하기
INSERT INTO TABLE_NOTNULL (LOGIN_ID, LOGIN_PWD)
VALUES ('TEST_ID_01','1234');
```

![](https://velog.velcdn.com/images/bebeco/post/83e46db8-1b2f-465e-8df8-0a3e9265c505/image.png)

▶︎ TEL 열은 별다른 제약 조건을 지정하지 않았으므로 값을 지정하지 않아도 오류가 발생하지 않는다.

---

- 열의 제약 조건으로 NOT NULL을 지정하면 UPDATE문을 사용하여 LOGIN_ID 또는 LOGIN_PWD 열 값을 NULL로 수정하는 것도 불가능하다. 제약 조건을 지정한 열은 항상 해당 제약 조건을 만족해야 하므로 신규 데이터의 삽입뿐만 아니라 기존 데이터의 수정 및 삭제에도 영향을 준다

```sql
// NOT NULL 제약 조건이 지정된 열 데이터를 NULL 값으로 업데이트하기
UPDATE TABLE_NOTNULL
   SET LOGIN_PWD = NULL
 WHERE LOGIN_ID = 'TEST_ID_01';
```

![](https://velog.velcdn.com/images/bebeco/post/f9450148-7af3-47e0-af20-89678b625a9a/image.png)

---
<br/>

> 제약 조건 확인

- 지정한 제약 조건 정보를 확인하려면 다음과 같은 USER_CONSTRAINTS 데이터 사전을 활용한다.

| 열 이름 | 설명 |
|:--------:|:-----------:|
| OWNER | 제약 조건 소유 계정|
| CONSTRAINT_NAME | 제약 조건 이름(직접 지정하지 않을 경우 오라클이 자동으로 지정함) |
| CONSTRAINT_TYPE | 제약 조건 종류<br/> C : CHECK, NOT NULL<br/> U : UNIQUE<br/> P : PRIMARY KEY<br/> R : FOREIGN KEY |
| TABLE_NAME | 제약 조건을 지정한 테이블 이름 |

```sql
// 제약 조건 살펴보기(SCOTT 계정)
 SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
   FROM USER_CONSTRAINTS;
```

![](https://velog.velcdn.com/images/bebeco/post/bc77276b-c107-43d6-b126-e57a906c3450/image.png)

▶︎ CONSTRAINT_NAME 열에 각 제약 조건 이름이 출력되며, 제약 조건의 이름을 따로 지정하지 않으면 오라클에 의해 자동으로 이름이 지정된다.
<br/>

> 제약 조건 이름 직접 지정

- 제약 조건에 이름을 직접 지정하려면 다음과 같이 CONSTRAINT 키워드를 사용한다.

```sql
// 테이블을 생성할 때 제약 조건에 이름 지정하기
CREATE TABLE TABLE_NOTNULL2(
    LOGIN_ID  VARCHAR2(20) CONSTRAINT TBLNN2_LGNID_NN NOT NULL,
    LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLNN2_LGNPW_NN NOT NULL,
    TEL       VARCHAR2(20)
);
```

![](https://velog.velcdn.com/images/bebeco/post/fe78ee6b-1b08-4717-8466-f5ce8520baee/image.png)

▶︎ 실무에서는 이름 붙이는 규칙을 정하여 제약 조건 이름을 직접 지정하는 경우가 많다.
<br/>

> 이미 생성한 테이블에 제약 조건 지정

- 제약 조건은 데이터베이스 사용 주기에서 비교적 초기에 지정하는 것이 일반적이다. 하지만 경우에 따라 이미 생성되어 있는 테이블에 제약 조건을 추가하거나 제약 조건을 변경 또는 삭제해야 하는 경우도 종종 생긴다.

#### 생성한 테이블에 제약 조건 추가하기

NOT NULL 제약 조건의 추가는 ALTER 명령어와 MODIFY 키워드를 사용한다.
```sql
// TEL 열에 NOT NULL 제약 조건 추가하기
ALTER TABLE TABLE_NOTNULL
MODIFY(TEL NOT NULL);
```

![](https://velog.velcdn.com/images/bebeco/post/ded75f50-83c5-4520-8dbe-38d94499284c/image.png)

▶︎ 열 데이터에 NULL을 허용하지 않는 NOT NULL 제약 조건을 추가하려고 했지만 오류가 발생하고 제약 조건 추가는 실패한다. 
이미 14-3에서 TEL 열에 NULL 삽입되어 있으므로 제약 조건이 추가되지 않은 것이다.
제약 조건 추가를 위한 명령어를 잘 작성했어도 제약 조건과 맞지 않는 데이터가 이미 있다면 제약 조건 지정에 실패한다.

```sql
// TEL 열 데이터 수정하기
UPDATE TABLE_NOTNULL
   SET TEL = '010-1234-5678'
 WHERE LOGIN_ID = 'TEST_ID_01';
 
 SELECT * FROM TABLE_NOTNULL;
```

![](https://velog.velcdn.com/images/bebeco/post/11d05500-002c-4e1a-80a0-ee6b997e3384/image.png)


```sql
// 실패했던 TEL 열에 NOT NULL 제약 조건 다시 지정하기
// NOT NULL 제약 조건 추가하기
 ALTER TABLE TABLE_NOTNULL
 MODIFY(TEL NOT NULL);
```

![](https://velog.velcdn.com/images/bebeco/post/cc9bbe94-e79a-42a0-9b0c-31d7fcb9f884/image.png)

▶︎ TABLE_NOTNULL 테이블의 TEL 열에 NULL을 가진 데이터가 없으므로 NOT NULL 제약 조건이 별다른 오류 없이 지정되는 것을 확인할 수 있다.
<br/>

### 생성한 테이블에 제약 조건 이름 직접 지정해서 추가하기

제약 조건 이름을 직접 지정하려면 CREATE와 마찬가지로 CONSTRAINT 키워드를 사용하면 된다.

```sql
 ALTER TABLE TABLE_NOTNULL2
 MODIFY(TEL CONSTRAINT TBLNN_TEL_NN NOT NULL);
 
  SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
    FROM USER_CONSTRAINTS;
```

![](https://velog.velcdn.com/images/bebeco/post/b1468284-1142-46f9-b537-b91a5ab35093/image.png)
<br/>

### 생성한 제약 조건의 이름 변경하기

이미 생성한 제약 조건 이름을 변경하려면 ALTER 명령어에 RENAME CONSTRAINT 키워드를 사용한다.

```sql
// 이미 생성된 제약 조건 이름 변경하기
ALTER TABLE TABLE_NOTNULL2
RENAME CONSTRAINT TBLNN_TEL_NN TO TBLNN2_TEL_NN;

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
  FROM USER_CONSTRAINTS;
```

![](https://velog.velcdn.com/images/bebeco/post/9642d93d-da14-47be-bcd6-5ac5541e92c0/image.png)

<br/>

> 제약 조건 삭제

- ALTER 명령어에 DROP CONSTRAINT 키워드를 사용하면 지정한 제약 조건을 삭제할 수 있다.

```sql
// 제약 조건 삭제하기
ALTER TABLE TABLE_NOTNULL2
DROP CONSTRAINT TBLNN2_TEL_NN;

DESC TABLE_NOTNULL2;
```

![](https://velog.velcdn.com/images/bebeco/post/2e98cdc8-90d1-420d-8be0-d8624fbdfb5f/image.png)

▶︎ 제약 조건을 삭제하면 TEL 열은 NULL이 저장될 수 있는 열이 된다.
<br/>

## 14 - 3 중복되지 않는 값 UNIQUE
<br/>

- UNIQUE 제약 조건은 열에 저장할 데이터의 중복을 허용하지 않고자 할 때 사용한다. NULL은 값이 존재하지 않음을 의미하기 때문에 중복 대상에서는 제외된다. 즉 UNIQUE 제약 조건을 지정한 열에 NULL은 여러 개 존재할 수 있다.
- UNIQUE 지정 방법은 NOT NULL 제약 조건과 동일하다.
<br/>

> 테이블을 생성하며 제약 조건 지정

- UNIQUE 제약 조건 역시 CREATE문으로 테이블을 생성할 때 지정할 수 있다.

```sql
// 제약 조건 지정하기(테이블을 생성할 때)
CREATE TABLE TABLE_UNIQUE(
    LOGIN_ID  VARCHAR2(20) UNIQUE,
    LOGIN_PWD VARCHAR2(20) NOT NULL,
    TEL       VARCHAR2(20)
);
```

<br/>

> 제약 조건 확인

- USER_CONSTRAINTS 데이터 사전에서 CONSTRAINT_TYPE 열 값이 U일 경우에 UNIQUE 제약 조건을 의미한다.

```sql
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
  FROM USER_CONSTRAINTS
WHERE TABLE_NAME = 'TABLE_UNIQUE';
```

![](https://velog.velcdn.com/images/bebeco/post/c9bf960b-0b6f-4dbf-a0fe-7cbefb719988/image.png)

<br/>

> 중복을 허락하지 않는 UNIQUE

- UNIQUE 제약 조건을 지정한 LOGIN_ID 열은 중복 값이 저장되지 않는다.

```sql
// TABLE_UNIQUE 테이블에 데이터 입력하기
INSERT INTO TABLE_UNIQUE(LOGIN_ID, LOGIN_PWD, TEL)
VALUES('TEST_ID_01', 'PWD01', '010-1234-5678');
```

![](https://velog.velcdn.com/images/bebeco/post/f4ec1481-57e7-486e-9801-c4115e4f6a6b/image.png)

```sql
// LOGIN_ID 열에 중복되는 데이터 넣기
INSERT INTO TABLE_UNIQUE(LOGIN_ID, LOGIN_PWD, TEL)
VALUES('TEST_ID_01', 'PWD01', '010-1234-5678');
```

![](https://velog.velcdn.com/images/bebeco/post/f3a2696f-3788-41ae-b387-64d970c94daa/image.png)

▶︎ UNIQUE 제약 조건이 지정되어 있는 LOGIN_ID 열 때문에 위와 같이 오류가 발생한다.

```sql
// TABLE_UNIQUE 테이블에 데이터 입력하기
INSERT INTO TABLE_UNIQUE(LOGIN_ID, LOGIN_PWD, TEL)
VALUES('TEST_ID_02', 'PWD01', '010-1234-5678');
```

![](https://velog.velcdn.com/images/bebeco/post/1c868475-70b2-49e7-849b-6d3588848cf5/image.png)

▶︎ LOGIN_ID에 다른 값을 지정한 경우는 문제없이 잘 실행된다.

<br/>

> UNIQUE 제약 조건과 NULL 값

- UNIQUE 제약 조건은 열 값의 중복은 허용하지 않지만 NULL 저장은 가능하다. NULL은 존재하지 않는 값 또는 해당 사항이 없다는 의미로 사용되는 특수한 값이므로 NULL과 NULL을 비교했을 때 값이 같은지를 확인할 수 없다.
- 즉 NULL은 데이터 중복의 의미를 부여할 수 없다.

```sql
// UNIQUE 제약 조건이 지정된 열에 NULL 값 입력하기
INSERT INTO TABLE_UNIQUE(LOGIN_ID, LOGIN_PWD, TEL)
VALUES(NULL, 'PWD01', '010-2345-6789');
```

![](https://velog.velcdn.com/images/bebeco/post/b28bbeba-4a77-4968-8b79-9f646b638797/image.png)

---

- UPDATE문을 사용하여 LOGIN_ID 열에 이미 존재하는 값을 지정할 경우에 실행 후 중복 데이터가 발생하므로 다음과 같이 실행되지 않는다.

```sql
UPDATE TABLE_UNIQUE
   SET LOGIN_ID='TEST_ID_01'
 WHERE LOGIN_ID IS NULL;
```

![](https://velog.velcdn.com/images/bebeco/post/034ad013-1a7d-4f53-813f-9064c0ebe7e8/image.png)

<br/>

> 테이블을 생성하며 제약 조건 이름 직접 지정

- UNIQUE 제약 조건 역시 제약 조건 이름을 지정할 수 있으며 지정하지 않으면 오라클이 자동으로 제약 조건 이름을 정해 준다.

```sql
// 테이블을 생성할 때 UNIQUE 제약 조건 설정하기
CREATE TABLE TABLE_UNIQUE2(
    LOGIN_ID  VARCHAR2(20) CONSTRAINT TBLUNQ2_LGNID_UNQ UNIQUE,
    LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLUNQ2_LGNPW_NN NOT NULL,
    TEL       VARCHAR2(20)
);

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
  FROM USER_CONSTRAINTS
 WHERE TABLE_NAME LIKE 'TABLE_UNIQUE%';
```

![](https://velog.velcdn.com/images/bebeco/post/894e0350-8a41-4c4c-a6ad-702d45e9fcaa/image.png)

<br/>

> 이미 생성한 테이블에 제약 조건 지정

- ALTER 명령어로 이미 생성되어 있는 테이블에 UNIQUE 제약 조건을 추가할 수 있다.

### 생성한 테이블에 제약 조건 추가하기

TABLE_UNIQUE 테이블의 TEL 열에 UNIQUE 제약 조건을 추가한다.

```sql
// 이미 생성한 테이블 열에 UNIQUE 제약 조건 추가하기
ALTER TABLE TABLE_UNIQUE
MODIFY(TEL UNIQUE);
```

![](https://velog.velcdn.com/images/bebeco/post/b931c6d8-30ac-4c42-afec-58a55055db2b/image.png)

▶︎ 오류가 발생하여 제약 조건 추가가 실패한다. 왜냐하면 TEL 열에 이미 중복된 열이 있기 때문이다. NOT NULL과 마찬가지로 제약 조건을 추가할 때 해당 열에 추가하려는 제약 조건에 맞지 않는 데이터가 존재할 경우 제약 조건 추가는 실행되지 못한다.
하지만 UPDATE문을 통해 TEL 열의 모든 값을 NULL로 수정한 후에 다시 ALTER문을 실행해 보면 문제없이 실행된다. UNIQUE는 NULL 중복 여부를 따지지 않기 때문이고, 또 NULL이 아닌 중복 값만 바꾸어도 결과는 같다.

```sql
// TEL 열 값을 모두 NULL 값으로 변경하기
UPDATE TABLE_UNIQUE
   SET TEL = NULL;
```

![](https://velog.velcdn.com/images/bebeco/post/5c63b3fd-4a08-4862-bfa0-e372429f7ab7/image.png)

```sql
// TEL 값에 UNIQUE 제약 조건 설정하기
ALTER TABLE TABLE_UNIQUE
MODIFY(TEL UNIQUE);
```

![](https://velog.velcdn.com/images/bebeco/post/c6fb32f9-3df9-4953-9352-a944ec4d6cdf/image.png)
<br/>

### 생성한 테이블에 제약 조건 이름 직접 지정하거나 바꾸기

UNIQUE 제약 조건 역시 이름을 직접 지정할 수 있으며 이후에 이름을 바꿀 수도 있다.

```sql
// UNIQUE 제약 조건 이름 직접 지정하기
ALTER TABLE TABLE_UNIQUE2
MODIFY(TEL CONSTRAINT TBLUNQ_TEL_UNQ UNIQUE);
 
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
  FROM USER_CONSTRAINTS
 WHERE TABLE_NAME LIKE 'TABLE_UNIQUE%'
```

![](https://velog.velcdn.com/images/bebeco/post/bcb7199f-2c82-4985-ba0f-76d6dd608ade/image.png)

```sql
// 이미 만들어져 있는 UNIQUE 제약 조건 이름 수정하기
ALTER TABLE TABLE_UNIQUE2
RENAME CONSTRAINT TBLUNQ_TEL_UNQ TO TBLUNQ2_TEL_UNQ;

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
  FROM USER_CONSTRAINTS
 WHERE TABLE_NAME LIKE 'TABLE_UNIQUE%';
```

![](https://velog.velcdn.com/images/bebeco/post/24df8db0-ab7f-4425-988e-c67fb893e76c/image.png)

<br/>

> 제약 조건 삭제

- UNIQUE 제약 조건 삭제 역시 ALTER 명령어에 DROP CONSTRAINT 키워드를 사용한다.

```sql
// 제약 조건 삭제하기
ALTER TABLE TABLE_UNIQUE2
DROP CONSTRAINT TBLUNQ2_TEL_UNQ;
```

![](https://velog.velcdn.com/images/bebeco/post/165ff814-f4da-42b2-81ca-e3a4c1b1705f/image.png)

<br/>

## 14 - 4 유일하게 하나만 있는 값 PRIMARY KEY

<br/>

- PRIMARY KEY 제약 조건은 UNIQUE와 NOT NULL 제약 조건의 특성을 모두 가지는 제약 조건이다. 즉 데이터 중복을 허용하지 않고 NULL도 허용하지 않는다.
- PRIMARY KEY 제약 조건을 테이블에 하나밖에 지정할 수 없다.
- 특정 열을 PRIMARY KEY로 지정하면 해당 열에는 자동으로 인덱스가 만들어진다.

⭐ 주민등록번호와 같은 예민한 개인 정보를 의미하는 데이터는 PRIMARY KEY로 지정하지 않는다.

<br/>

> 테이블을 생성하며 제약 조건 지정하기

- 테이블 PRIMARY KEY(기본키) 제약 조건은 앞에서 살펴본 제약 조건처럼 CREATE문으로 테이블을 생성하면서 지정할 수 있다.

```sql
// 테이블을 생성할 때 특정 열에 PRIMARY KEY 설정하기
CREATE TABLE TABLE_PK(
    LOGIN_ID  VARCHAR2(20) PRIMARY KEY,
    LOGIN_PWD VARCHAR2(20) NOT NULL,
    TEL       VARCHAR2(20)
);
```

![](https://velog.velcdn.com/images/bebeco/post/3fe01a4d-7a90-4301-8d79-137682313297/image.png)

```sql
// 생성한 PRIMARY KEY 확인하기
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
  FROM USER_CONSTRAINTS
 WHERE TABLE_NAME LIKE 'TABLE_PK%';
```

![](https://velog.velcdn.com/images/bebeco/post/ae7fb91e-dd2b-46b2-b559-1ceda2c02628/image.png)

```sql
// 생성한 PRIMARY KEY를 통해 자동 생성된 INDEX 확인하기
SELECT INDEX_NAME, TABLE_OWNER, TABLE_NAME
  FROM USER_INDEXES
 WHERE TABLE_NAME LIKE 'TABLE_PK%';
```

![](https://velog.velcdn.com/images/bebeco/post/a11e4f95-ddb2-4c8c-8df3-21f80939465c/image.png)

<br/>

> 테이블을 생성하며 제약 조건 이름 직접 지정하기

- PRIMARY KEY 역시 제약 조건의 이름을 직접 지정할 수 있다.

```sql
// 제약 조건의 이름을 직접 지정하여 테이블 생성하기
CREATE TABLE TABLE_PK2(
    LOGIN_ID  VARCHAR2(20) CONSTRAINT TBLPK2_LGNID_PK PRIMARY KEY,
    LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLPK2_LGNPW_NN NOT NULL,
    TEL       VARCHAR2(20)
);
```

![](https://velog.velcdn.com/images/bebeco/post/fb055cd1-64ba-407c-9baf-4e3d4f8adae5/image.png)

<br/>

> PRIMARY KEY 제약 조건을 지정한 열 확인(중복 값을 입력했을 때)

- PRIMARY KEY 제약 조건을 지정한 열에는 중복 값과 NULL이 허용되지 않는다.

```sql
// TABLE_PK 테이블에 데이터 입력하기
INSERT INTO TABLE_PK(LOGIN_ID, LOGIN_PWD, TEL)
VALUES('TEST_ID_01', 'PWD01', '010-1234-5678');
```

![](https://velog.velcdn.com/images/bebeco/post/49fb82ea-f3a4-43d3-9fc2-066c63e1b6a1/image.png)

```sql
// TABLE_PK 테이블에 중복되는 데이터 입력하기
INSERT INTO TABLE_PK(LOGIN_ID, LOGIN_PWD, TEL)
VALUES('TEST_ID_01', 'PWD02', '010-2345-6789');
```

![](https://velog.velcdn.com/images/bebeco/post/23eb144d-6160-4c21-9451-a24f3c68f55c/image.png)

<br/>

> PRIMARY KEY 제약 조건을 지정한 열 확인(NULL 값을 입력했을 때)

```sql
// NULL 값을 명시적으로 입력하기
INSERT INTO TABLE_PK(LOGIN_ID, LOGIN_PWD, TEL)
VALUES(NULL, 'PWD02', '010-2345-6789');
```

![](https://velog.velcdn.com/images/bebeco/post/c6848f88-dc74-4c26-be33-0bd61299fb13/image.png)

```sql
// NULL 값을 암시적으로 입력하기
INSERT INTO TABLE_PK(LOGIN_PWD, TEL)
VALUES('PWD02', '010-2345-6789');
```

![](https://velog.velcdn.com/images/bebeco/post/324267e5-bce6-491d-ad4a-abcb0debb8aa/image.png)

- PRIMARY KEY 제약 조건 역시 ALTER문의 MODIFY, RENAME, DROP을 통해 추가·수정·이름 변경·삭제 등의 수행이 가능하다.

---
