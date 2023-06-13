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

## Test
설치가 완료된 후 Django 프로젝트로 돌아가서, 하위의 명령어를 입력하고 Observability -> APM -> Service에서 프로젝트가 뜨는지 확인합니다.

> $ python manage.py elasticapm test

```
.env settings

ELASTIC_VERSION=8.6.2

## Passwords for stack users
#

# User 'elastic' (built-in)
#
# Superuser role, full access to cluster management and data indices.
# https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html
ELASTIC_PASSWORD='changeme'

# User 'logstash_internal' (custom)
#
# The user Logstash uses to connect and send data to Elasticsearch.
# https://www.elastic.co/guide/en/logstash/current/ls-security.html
LOGSTASH_INTERNAL_PASSWORD='changeme'

# User 'kibana_system' (built-in)
#
# The user Kibana uses to connect and communicate with Elasticsearch.
# https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html
KIBANA_SYSTEM_PASSWORD='changeme'

# Users 'metricbeat_internal', 'filebeat_internal' and 'heartbeat_internal' (custom)
#
# The users Beats use to connect and send data to Elasticsearch.
# https://www.elastic.co/guide/en/beats/metricbeat/current/feature-roles.html
METRICBEAT_INTERNAL_PASSWORD=''
FILEBEAT_INTERNAL_PASSWORD=''
HEARTBEAT_INTERNAL_PASSWORD=''

# User 'monitoring_internal' (custom)
#
# The user Metricbeat uses to collect monitoring data from stack components.
# https://www.elastic.co/guide/en/elasticsearch/reference/current/how-monitoring-works.html
MONITORING_INTERNAL_PASSWORD=''

# User 'beats_system' (built-in)
#
# The user the Beats use when storing monitoring information in Elasticsearch.
# https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html
BEATS_SYSTEM_PASSWORD=''

```