다음 명령으로 DB 마이그레이션과 계정을 생성한다.

docker-compose up airflow-init

아래와 같이 출력이 되면 정상적으로 초기화된 것이라고 한다. 내 경우에는 마지막에 TypeError: **init**() got an unexpected keyword argument 'disable_buffering' 에러가 같이 출력되기는 했지만 결과적으로 Airflow를 실행하는데는 문제 없었다. 구글링해도 비슷한 사례가 없어서 원인은 알 수 없다. (아시는 분은 댓글 부탁드립니다.)

airflow-init_1       | Upgrades done
airflow-init_1       | Admin user airflow created
airflow-init_1       | 2.1.3
start_airflow-init_1 exited with code 0
최초 계정의 id는 airflow 이고, password도 airflow 이다.



환경 초기화
공식문서에 따르면 이렇게 docker를 이용해 airflow를 설치하는 과정은 가장 빠른 방법일 뿐, 프로덕션 환경에서 사용하기 위한 것이 아니라고 경고하고 있다. 뭔가 문제가 생겼을 경우 다 지우고 처음부터 다시 시작하길 권장하고 있다. 아래는 다 지우는 방법이다.

# docker-compose.yaml 파일이 있는 경로에서 다음 실행
docker-compose down --volumes --remove-orphans

# docker-compose.yaml 파일 위치한 경로 제거
rm -rf <DIRECTORY>
환경 초기화 후에는 다시 docker-compose.yaml 을 다운받는 과정부터 시작하면 된다.



Airflow 시작하기
docker-compose up -d # -d: 백그라운드 실행
아래와 같이 컨테이너들이 잘 실행된 것을 확인할 수 있다.

docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED        STATUS                   PORTS                                                  NAMES
a17017bbd550   apache/airflow:2.1.3   "/usr/bin/dumb-init …"   2 hours ago    Up 2 hours (unhealthy)   8080/tcp                                               airflow_airflow-worker_1
b6869440964d   apache/airflow:2.1.3   "/usr/bin/dumb-init …"   2 hours ago    Up 2 hours (unhealthy)   8080/tcp                                               airflow_airflow-scheduler_1
9a4c899739c8   apache/airflow:2.1.3   "/usr/bin/dumb-init …"   2 hours ago    Up 2 hours (healthy)     0.0.0.0:5555->5555/tcp, :::5555->5555/tcp, 8080/tcp    airflow_flower_1
24c3823cbcb4   apache/airflow:2.1.3   "/usr/bin/dumb-init …"   2 hours ago    Up 2 hours (healthy)     0.0.0.0:8080->8080/tcp, :::8080->8080/tcp              airflow_airflow-webserver_1
3162f333503e   postgres:13            "docker-entrypoint.s…"   2 hours ago    Up 2 hours (healthy)     5432/tcp                                               airflow_postgres_1
2ca33663a87d   redis:latest           "docker-entrypoint.s…"   2 hours ago    Up 2 hours (healthy)     0.0.0.0:6379->6379/tcp, :::6379->6379/tcp              airflow_redis_1


https://wooiljeong.github.io/server/docker-airflow/