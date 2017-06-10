# docker-compose-example

An example of directory layout in Docker Compose

## Phase 1: Only YAML

```
$ docker-compose up -d
$ mysql -h 127.0.0.1 -P 3306 -u root -pmy-secret-pw -e "SELECT version();"
Warning: Using a password on the command line interface can be insecure.
+-----------+
| version() |
+-----------+
| 5.7.18    |
+-----------+
$ PGPASSWORD=mysecretpassword psql -h 127.0.0.1 -p 5432 -U postgres -c "SELECT version();"
                                         version
------------------------------------------------------------------------------------------
 PostgreSQL 9.6.2 on x86_64-pc-linux-gnu, compiled by gcc (Debian 4.9.2-10) 4.9.2, 64-bit
(1 row)

$ docker-compose stop && docker-compose rm -f
```

## Phase 2: YAML with data volume

```
$ docker-compose up -d
$ curl 127.0.0.1
<!DOCTYPE html>
<html>
<head>
    <title>docker-compose-example</title>
</head>
<body>
<h1>This is an example of docker compose!</h1>
</body>
</html>

# Wait for a while

$ docker-compose logs fluentd | tail -1
fluentd_1  | 2017-04-22 09:46:50.000000000 +0000 docker.phase2_nginx_1: {"container_id":"...","container_name":"/phase2_nginx_1","source":"stdout","log":"x.x.x.x - - [22/Apr/2017:09:46:50 +0000] \"GET / HTTP/1.1\" 200 150 \"-\" \"curl/7.51.0\" \"-\""}
$ docker-compose stop && docker-compose rm -f
```

## Phase 3: Dockerfile for each service

```
$ docker-compose up -d
$ curl -s 127.0.0.1
...
$ seq 1 10 | parallel curl -s 127.0.0.1 > /dev/null
$ seq 1 10 | parallel curl -s 127.0.0.1/notfound > /dev/null
$ seq 1 5 | parallel curl -s -X POST 127.0.0.1 > /dev/null

# Wait for a while

$ mysql -h 127.0.0.1 -P 3306 -u root -pmy-secret-pw test -e "SELECT * from demo_test;"
Warning: Using a password on the command line interface can be insecure.
+-------------------+--------------+------+------+------+
| c1                | c2           | c3   | c4   | c5   |
+-------------------+--------------+------+------+------+
| AA                | HELLO, HELLO |    8 |    0 |    0 |
| GET_/notfound_404 | 10           |    0 |   22 |    0 |
| GET_/_200         | 10           |    0 |   11 |    0 |
| POST_/_405        | 5            |    0 |   28 |    0 |
+-------------------+--------------+------+------+------+
```
