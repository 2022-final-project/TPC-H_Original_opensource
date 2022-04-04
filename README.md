Ubuntu 20.04 환경에서 TPC-H 를 통해 Tables, Datas, Quries 를 받는다.

이 TPC-H 를 통해 확보한 Data들을 postgreSQL 을 통하여

CREATE TABLES, INSERT DATAS, Query Optimization 을 진행한다.

첨부된 File 들은 postgreSQL 환경에서 사용할 수 있도록 수정이 된 Open source 이다.

|문제점|
|-----|
|22개의 쿼리에는 postgreSQL에서 실행할 수 없는 형태의 쿼리들이 담겨있다|

# 설치 과정

> root# cd dbgen

> root# cp makefile.suite makefile

> root# vi makefile
      
      CC = gcc
      DATABASE = ORACLE
      MACHINE = LINUX
      WORDLOAD = TPCH
      
  와 같이 수정
  
> root# make

> root# ./dbgen -s 10

      뒤 숫자의 크기로 Data 들의 개수를 정할 수 있다.
      
> root# for i in \`ls *.tbl\`; do sed 's/|$//' $i > ${i/tbl/csv}; echo $i; done

      "*.tbl" 파일들을 "*csv" 파일로 변경하면서 가장 마지막 Delimiter 인 | 를 제거한다.
      
> root# su postgres

> postgres# createdb tpch

> postgres# git clone https://github.com/tvondra/pg_tpch.git

> postgres# cd pg_tpch/dss
      
      방금 깃클론 사이트에 들어가보면 pg_tpch/dss directory 가 있다.
      
> postgres# psql tpch < tpch-create.sql

      8개의 테이블들을 생성한다.
      
> postgres# psql -d tpch

      postgres 상에 진입하게 된다.
      
## 8개의 Table 들에 Data 들을 INSERT

> psql$ COPY part FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/part.csv' WITH (FORMAT csv, DELIMITER '|');

> psql$ COPY region FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/region.csv' WITH (FORMAT csv, DELIMITER '|');

> psql$ COPY nation FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/nation.csv' WITH (FORMAT csv, DELIMITER '|');

> psql$ COPY supplier FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/supplier.csv' WITH (FORMAT csv, DELIMITER '|');

> psql$ COPY customer FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/customer.csv' WITH (FORMAT csv, DELIMITER '|');

> psql$ COPY partsupp FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/partsupp.csv' WITH (FORMAT csv, DELIMITER '|');

> psql$ COPY orders FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/orders.csv' WITH (FORMAT csv, DELIMITER '|');

> psql$ COPY lineitem FROM '/절대경로/TPC-H_Tools_v3.0.0/dbgen/lineitem.csv' WITH (FORMAT csv, DELIMITER '|');

## postgres 종료 후 PRIMARY KEY생성 및 테이블 간 관계 설정 (FK)

> PRESS ctrl + z

> postgres# psql tpch < tpch-pkeys.sql

> postgres# psql tpch < tpch-alter.sql
