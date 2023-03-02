# Elastic stack (ELK) on Docker
> **Note**  
> ELK 버전: 8.6.2
> ELK + apm-server를 통합하여 하나의 docker-compose.yml로 묶었습니다.

### **초기 Kibana 접속 시 아이디 패스워드**
* user: *elastic*
* password: *changeme*

## Configuration
apm-server 설정은 apm-server/config/apm-server.yml에 있습니다.

ELK 버전 및 초기 비밀번호 설정은 .env 파일에 존재합니다.

docker-compose.yml로 빌드를 진행하고, 각각의 서비스(Elasticsearch, Kibana, Logstash, APM-Server)는 모두 Network로 연결되어 각 컨테이너간 통신이 가능하게 처리했습니다.

> $ docker-compose up -d

> 각 서비스의 기본 포트 정보는 아래와 같습니다.
* logstash - localhost:9600
* kibana - localhost:5601
* elasticsearch - localhost:9200
* apm-server - localhost:8200

> **Note**  
**설정이 완료된 후 apm-server로 접속하여, publish_ready: true인지 반드시 확인합니다.
publish_ready가 false인 경우 elasticsearch와 통신이 불가합니다.**