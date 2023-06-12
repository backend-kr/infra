> 내부망에서 push를 하려해도 안되길래 

설정 파일 (/etc/docker/daemon.json)을 다음과 같이 수정(허용할 주소만 열어줌)
{
  "insecure-registries": ["123.123.123.123:5000"]
}

sudo systemctl restart docker


* MacOS에서 해결방법
```
1. Docker Desktop 혹은 home 디렉토리에서 docker로 들어간다.(나는 Docker Desktop으로 함)
2. 설정으로 들어가서 Docker Engine 클릭!
3. "insecure-registries": ["ip주소:port"] # docker-registry 포트 입력!!

docker-compose file에 의하면 현재 5000번이 registry고, 8089는 ui로 설정되어있음
```