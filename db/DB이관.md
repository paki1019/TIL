# DB이관

DB에 있는 데이터를 다른 데이터베이스로 옮기는 작업
mysql은 mysqldump 명령어를 사용하여 백업 > 복원 과정으로 이관함

```
mysqldump [options] db_name [tbl_name ...]
mysqldump [options] --databases db_name ...
mysqldump [options] --all-databases
```

백업 명령어

```
mysqldump -uroot -p ${db_name} > ${dump.sql}
```

복원 명령어

```
mysqldump -uroot -p ${db_name} < ${dump.sql}
```

위와 같이 CLI 환경을 사용할 수도 있지만 workbench의 data export/data import 기능을 사용할 수도 있다.
