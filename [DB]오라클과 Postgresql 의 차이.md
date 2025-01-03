# 오라클과 PostgreSQL의 차이점

## 1. 기본 특징 비교

### Oracle
- 상업용 라이선스 (유료)
- 대규모 엔터프라이즈 환경에 최적화
- 강력한 기술 지원
- 높은 안정성과 성능

### PostgreSQL
- 오픈소스 (무료)
- 커뮤니티 기반 개발
- 확장성이 뛰어남
- ACID 준수

## 2. 문법 차이점

### 1) 시퀀스 생성
```sql
-- Oracle
CREATE SEQUENCE sequence_name
START WITH 1
INCREMENT BY 1;

-- PostgreSQL
CREATE SEQUENCE sequence_name
START 1
INCREMENT 1;
```

### 2) 자동 증가 컬럼
```sql
-- Oracle
-- 12c 이전 버전: 시퀀스 사용
CREATE TABLE table_name (
    id NUMBER PRIMARY KEY,
    ...
);

-- 12c 이후 버전
CREATE TABLE table_name (
    id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    ...
);

-- PostgreSQL
CREATE TABLE table_name (
    id SERIAL PRIMARY KEY,
    ...
);
```

### 3) DUAL 테이블
```sql
-- Oracle
SELECT SYSDATE FROM DUAL;

-- PostgreSQL
SELECT CURRENT_TIMESTAMP;  -- DUAL 테이블 불필요
```

### 4) 문자열 연결
```sql
-- Oracle
SELECT column1 || column2 FROM table_name;

-- PostgreSQL
SELECT column1 || column2 FROM table_name;
-- 또는
SELECT CONCAT(column1, column2) FROM table_name;
```

## 3. 주요 함수 차이점

### 1) 날짜 함수
```sql
-- Oracle
SELECT SYSDATE FROM DUAL;                    -- 현재 날짜
SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD') FROM DUAL;  -- 날짜 포맷팅

-- PostgreSQL
SELECT CURRENT_DATE;                         -- 현재 날짜
SELECT TO_CHAR(CURRENT_DATE, 'YYYY-MM-DD');  -- 날짜 포맷팅
```

### 2) NULL 처리
```sql
-- Oracle
SELECT NVL(column_name, '대체값') FROM table_name;

-- PostgreSQL
SELECT COALESCE(column_name, '대체값') FROM table_name;
```

### 3) ROWNUM과 LIMIT
```sql
-- Oracle
SELECT * FROM table_name WHERE ROWNUM <= 10;

-- PostgreSQL
SELECT * FROM table_name LIMIT 10;
```

## 4. 데이터 타입 차이

### Oracle
- NUMBER
- VARCHAR2
- DATE
- TIMESTAMP
- CLOB
- BLOB

### PostgreSQL
- INTEGER
- NUMERIC
- VARCHAR
- TEXT: 길이 제한이 없는 가변길이 문자열 타입
  - VARCHAR과 달리 길이 제한이 없음
  - 대용량 텍스트 데이터 저장에 적합
  - 자동 압축으로 저장 공간 효율적
  - 예: 게시글 본문, 로그 데이터 등
- TIMESTAMP
- BYTEA: Binary 데이터를 저장하는 타입
  - 이미지, 문서, 압축파일 등 바이너리 데이터 저장
  - 최대 1GB까지 저장 가능
  - 예시:
    ```sql
    -- 바이너리 데이터 저장
    CREATE TABLE documents (
        id SERIAL PRIMARY KEY,
        file_name VARCHAR(255),
        file_data BYTEA
    );
    ```
- JSON/JSONB (네이티브 JSON 지원)
  - JSON: 텍스트 형식으로 저장, 매번 파싱 필요
  - JSONB: 바이너리 형식으로 저장, 처리 속도 빠름
  - JSONB의 장점:
    - 인덱싱 지원
    - 중복 키 제거
    - 객체 키 순서 보존하지 않음
  - 예시:
    ```sql
    -- JSON 컬럼 사용 예
    CREATE TABLE users (
        id SERIAL PRIMARY KEY,
        data JSON,
        profile JSONB
    );
    
    -- JSON 데이터 쿼리
    SELECT data->'name' AS name
    FROM users
    WHERE data->>'age' = '25';
    
    -- JSONB 인덱스 생성
    CREATE INDEX idx_profile ON users USING GIN (profile);
    ```

## 5. 특징적인 기능

### Oracle
- 머티리얼라이즈드 뷰 (Materialized View)
  - 쿼리 결과를 물리적으로 저장하여 성능 향상
  - 주기적으로 데이터 리프레시 가능
  - 예시:
    ```sql
    CREATE MATERIALIZED VIEW mv_sales_summary
    REFRESH ON DEMAND
    AS
    SELECT product_id, SUM(quantity) total_quantity
    FROM sales
    GROUP BY product_id;
    ```

- 계층형 쿼리 (CONNECT BY)
  - 트리 구조의 데이터를 쉽게 조회
  - 조직도, 게시판 답글 등에 활용
  - 예시:
    ```sql
    SELECT LEVEL, employee_id, manager_id, name
    FROM employees
    START WITH manager_id IS NULL
    CONNECT BY PRIOR employee_id = manager_id;
    ```

- 파티셔닝
  - 테이블을 물리적으로 분할하여 관리
  - 범위, 리스트, 해시 파티셔닝 지원
  - 예시:
    ```sql
    CREATE TABLE sales (
        sale_id NUMBER,
        sale_date DATE,
        amount NUMBER
    )
    PARTITION BY RANGE (sale_date) (
        PARTITION sales_2023 VALUES LESS THAN (TO_DATE('2024-01-01', 'YYYY-MM-DD')),
        PARTITION sales_2024 VALUES LESS THAN (TO_DATE('2025-01-01', 'YYYY-MM-DD'))
    );
    ```

- RAC (Real Application Clusters)
  - 여러 서버에서 하나의 데이터베이스 운영
  - 고가용성과 로드 밸런싱 제공
  - 무중단 서비스 가능

### PostgreSQL
- 네이티브 JSON 지원
  - JSON 및 JSONB 타입 제공
  - 강력한 JSON 처리 함수들
  - 예시:
    ```sql
    -- JSON 배열 처리
    SELECT jsonb_array_elements(data->'items')
    FROM orders;
    
    -- JSON 객체 생성
    SELECT jsonb_build_object(
        'name', name,
        'age', age,
        'address', address
    ) FROM users;
    ```

- 사용자 정의 데이터 타입
  - 복합 타입(Composite Types) 생성 가능
  - 열거형(ENUM) 타입 지원
  - 예시:
    ```sql
    -- 복합 타입 생성
    CREATE TYPE address_type AS (
        street VARCHAR(100),
        city VARCHAR(50),
        country VARCHAR(50)
    );
    
    -- ENUM 타입 생성
    CREATE TYPE status_type AS ENUM (
        'pending',
        'processing',
        'completed',
        'cancelled'
    );
    ```

- 테이블 상속
  - 테이블 간 상속 관계 설정 가능
  - 파티셔닝 구현에 활용
  - 예시:
    ```sql
    CREATE TABLE cities (
        name text,
        population float,
        elevation int
    );
    
    CREATE TABLE capitals (
        state char(2)
    ) INHERITS (cities);
    ```

- GiST 인덱스 (Generalized Search Tree)
  - 지리정보 데이터 처리
  - 전문 검색(Full-text search)
  - 커스텀 데이터 타입 인덱싱
  - 예시:
    ```sql
    -- 지리정보 인덱스
    CREATE INDEX idx_locations ON cities 
    USING GIST (location);
    
    -- 전문 검색 인덱스
    CREATE INDEX idx_document ON documents 
    USING GIST (to_tsvector('english', content));
    ```

- 다양한 확장 기능
  - PostGIS: 공간 데이터 처리
  - pg_stat_statements: SQL 모니터링
  - uuid-ossp: UUID 생성
  - pgcrypto: 암호화 기능
  - 예시:
    ```sql
    -- 확장 기능 설치
    CREATE EXTENSION postgis;
    CREATE EXTENSION pg_stat_statements;
    
    -- PostGIS 사용 예
    SELECT ST_Distance(
        ST_GeomFromText('POINT(0 0)'),
        ST_GeomFromText('POINT(3 4)')
    );
    ```

## 6. 성능 최적화

### Oracle
- Cost-Based Optimizer (CBO)
- 힌트(Hint) 사용 가능
- 통계 정보 관리가 중요

### PostgreSQL
- EXPLAIN ANALYZE
- 자동 VACUUM
- 다양한 인덱스 타입 지원
- 병렬 쿼리 처리
