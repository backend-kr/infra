# Redis Master-Slave 구성
이 프로젝트에서는 Redis를 Master-Slave 구성으로 사용하고 있습니다. 이를 통해 데이터 복제와 읽기 요청의 분산 처리를 할 수 있습니다.

# Docker Compose 설정

docker-compose.yml 파일에서는 각 서비스에 'ports' 설정을 추가하여, 외부에서 컨테이너로의 네트워크 액세스를 허용하고 있습니다.

다음은 Redis Master와 Slave 설정 예시입니다:

```yml
version: '3'

services:
  master:
    image: redis:latest
    container_name: redis-master
    command: redis-server --appendonly yes
    volumes:
      - redis-data:/data
    ports:
      - 6379:6379

  slave:
    image: redis:latest
    container_name: redis-slave
    command: redis-server --slaveof master 6379 --appendonly yes
    volumes:
      - redis-data:/data
    ports:
      - 6380:6379
    depends_on:
      - master

volumes:
  redis-data:

이 설정에서 ports의 첫 번째 숫자는 Docker host의 포트를, 
두 번째 숫자는 컨테이너 내의 포트를 나타냅니다. 

따라서 master는 6379 포트로, slave는 6380 포트로 외부에서 접근 가능하게 됩니다.
```