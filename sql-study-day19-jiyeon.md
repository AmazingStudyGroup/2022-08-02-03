## 14 제약조건
- 제약조건은 테이블에 저장할 테이터를 제약하는 특수한 규칙을 뜻한다.     
- 제약 조건을 설정한 열에는 조건에 맞지 않는 데이터를 저장할 수 없다.      
- 데이터 정의로(DDL)로 제약조건을 설정할 수 있다.     

### 14-1 제약 조건 종류
#### 제약 조건이란?
오라클에서 사용하는 제약 조건은 테이블의 특정 열에 지정한다.     
제약 조건을 지정한 열에 제약 조건에 부합하지 않는 데이터를 저장할 수 없다.     

|종류|설명|
|--|---|
|NOT NULL|지정한 열에 NULL을 허용하지 않는다. NULL을 제외한 데이터의 중복은 허용|
|UNIQUE | 지정한 열이 유일한 값을 가져야 한다. 즉 중복될 수 없다. 단, NULL은 값의 중복에서 제외|
|PRIMARY KEY|지정한 열이 **유일한 값이면서 NULL을 허용하지 않는다.** PRIMARY KEY는 테이블에 하나만 지정 가능|
|FOREIGN KEY|다른 테이블의 열을 참조하여 **존재하는 값만** 입력 가능|
|CHECK |설정한 조건식을 만족하는 데이터만 입력 가능 |

- 데이터 무결성(data integrity) : 데이터베이스에 저장되는 데이터의 정확성과 일관성을 보장     

|종류|설명|
|---|---|
|영역 무결성(domain integrity)|열에 저장되는 값의 적정 여부를 확인. 자료형, 적절한 형식의 데이터, NULL여부 같은 정해 놓은 범위를 만족하는 데이터임을 규정|
|개체 무결성(entity integrity)|테이블 데이터를 유일하게 식별할 수 있는 기본키는 반드시 값을 가지고 있어야 하며 NULL이 될 수 없고 중복될 수도 없음을 규정|
|참조 무결성(referential integrity)|참조 테이블의 외래키 값은 참조 테이블의 기본키로서 존재해야 하며 NULL이 가능|


### 14-2 빈값을 허락하지 않는 NOT NULL
#### 테이블을 생성하며 제약 조건 지정
NOT NULL은 특정 열에 데이터의 중복 여부와는 상관없이 NULL의 저장을 허용하지 않는 제약 조건이다     
```SQL
CREATE TABLE TABLE_NOTNULL(
    LOGIN_ID  VARCHAR(20) NOT NULL,
    LOGIN_PWD VARCHAR(20) NOT NULL,
    TEL       VARCHAR(20)
);
```
> 신규 데이터 삽입뿐만 아니라 기존 데이터의 수정 및 삭제에도 제약 조건이 영향을 준다.      
> (제약조건 미충족시 오류발생)    

#### 제약 조건 확인
**USER_CONSTRAINTS** 데이터 사전을 활용하여 지정한 제약 조건 정보를 확인할 수 있다.    

`OWNER` : 제약 조건 소유 계정      
`CONSTRAINT_NAME` : 제약 조건 이름(직접 지정하지 않을 경우 오라클이 자동으로 지정함)     
`CONSTRAINT_TYPE` : 제약 조건 종류       
- C : CHECK, NOT NULL     
- U : UNIQUE    
- P : PRIMARY KEY    
- R : FOREIGN KEY     

`TABLE_NAME` : 제약 조건을 지정한 테이블 이름      

#### 제약 조건 이름 직접 지정 
```SQL
CREATE TABLE TABLE_NOTNULL2(
    LOGIN_ID  VARCHAR2(20) CONSTRAINT TBLNN2_LGNID)_NN NOT NULL,
    LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLNN2_LGNPW)_NN NOT NULL,
    TEL       VARCHAR2(20)
);
```

#### 이미 생성한 테이블에 제약 조건 지정
> (주의) 제약 조건 대상이 되는 열이 가진 데이터 중 추가하려는 제약 조건에 맞지 않는 데이터가 존재하면 오류가 난다.     

```SQL
-- NULL값이 이미 존재하는 TEL열에 NOT NULL 제약조건 추가하기(오류남)
ALTER TABLE TABLE_NOTNULL
MODIFY(TEL NOT NULL);

-- TEL열 데이터 수정하기
UPDATE TABLE_NOTNULL
   SET TEL = '010-1234-5678'
 WHERE LOGIN_ID = 'TEST_ID_01';

-- NOT NULL 제약 조건이 정상적으로 추가됨
ALTER TABLE TABLE_NOTNULL
MODIFY(TEL NOT NULL);

-- 제약 조건 삭제
ALTER TABLE TABLE_NOTNULL2
DROP CONSTRAINT TBLNN2_TEL_NN;
```

### 14-3 중복되지 않는 값 UNIQUE
- 앞서 살펴본 NOT NULL을 특정 열에 지정하는 방법과 매우 유사하다.      
- 한가지 기억할 부분은, NULL값의 경우 '값이 없음'을 의미하기 때문에 UNIQUE 제약 조건이 걸린 열이라고 하더라도
**NULL값이 여러개 존재할 수 있다.**           


### 14-4 유일하게 하나만 있는 값 PRIMARY KEY

####

####

