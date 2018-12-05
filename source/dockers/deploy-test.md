title: Deploy Test
---

# Deploy Test

```sh
$ scp -r hanzo@10.122.22.115:/home/hanzo/Workspace/MicroServices/esp-framework .
$ scp hanzo@10.122.22.115:/home/hanzo/Workspace/MicroServices/esp-framework/esp-gateway/target/gateway-0.0.1-SNAPSHOT.jar .
```

## Runing Mode

- `maven`
```sh
$ mvn spring-boot:run \
  -DCFG_CONFIG_HOST=10.122.22.115 \
  -Deureka.client.serviceUrl.defaultZone=http://10.122.22.115:8761/eureka
```

- `java`
```sh
$ java -jar gateway-0.0.1-SNAPSHOT.jar \
  --CFG_CONFIG_HOST=10.122.22.115 \
  --eureka.client.serviceUrl.defaultZone=http://10.122.22.115:8761/eureka	
```

- `docker`
```sh
$ mvn clean package -Dmaven.test.skip=true docker:build

$ docker run -d --name esp-registry -p 8761:8761 10.122.22.115:5000/esp-registry

$ docker run -d --name esp-config -p 8750:8750 \
	-e CFG_EUREKA_HOST=10.122.22.115 \
	10.122.22.115:5000/esp-config \
	; docker logs -f esp-gateway

$ docker run -d --name esp-gateway -p 8765:8765 \
	-e CFG_CONFIG_HOST=10.122.22.115 \
	-e SPRING_CLOUD_CONFIG_LABEL=env \
	10.122.22.115:5000/esp-gateway \
	; docker logs -f esp-gateway

$ docker run -d --name esp-admin -p 8085:8085 \
	-e CFG_CONFIG_HOST=10.122.22.115 \
	-e SPRING_CLOUD_CONFIG_LABEL=env \
	10.122.22.115:5000/esp-admin \
	; docker logs -f esp-admin
```

## Docker Command

- Create tag
```sh
$ docker tag 10.122.22.115:5000/esp-registry 10.122.22.115:5000/esp-registry
$ docker tag 10.122.22.115:5000/esp-config 10.122.22.115:5000/esp-config
$ docker tag 10.122.22.115:5000/esp-gateway 10.122.22.115:5000/esp-gateway
$ docker tag 10.122.22.115:5000/esp-admin10.122.22.115:5000/esp-admin
```

- Remove container
```sh
$ docker rm -v $(docker stop esp-registry)
$ docker rm -v $(docker stop esp-config)
$ docker rm -v $(docker stop esp-gateway)
$ docker rm -v $(docker stop esp-admin)
#! or
$ docker ps -a --filter "name=esp-*"
$ docker rm -v $(docker ps -a --filter "name=esp-*" -q)
```

- Remove image
```sh
$ docker images -f "reference=10.122.22.115:5000/esp-*"

$ docker rmi $(docker images 10.122.22.115:5000/* -q)
#! or
$ docker rmi -f $(docker images -f "reference=10.122.22.115:5000/esp-*" -q)
```

## Docker Settings

- /etc/systemd/system/docker.service.d/http-proxy.conf
```
[Service]
Environment="HTTP_PROXY=http://proxy1.bj.petrochina:8080"
Environment="HTTPS_PROXY=http://proxy1.bj.petrochina:8080" "NO_PROXY=localhost, 127.0.0.1, 10.27.213.66, 10.27.213.69, 10.122.22.115"
```

- /etc/docker/daemon.json
```
{
  "registry-mirrors": ["https://ik8akj45.mirror.aliyuncs.com"],
  "insecure-registries": ["10.27.213.66:5000", "10.122.22.115:5000"]
}
```

- /var/lib/docker/containers/CONTAINER_ID/hostconfig.json
```
# update port mapping
{
	...
	"PortBindings":{"5000/tcp":[{"HostIp":"","HostPort":"5000"}]}
	...
}
```

- http: server gave HTTP response to HTTPS client
```sh
$ vim /etc/docker/daemon.json
{ 
	"insecure-registries":["10.122.22.115:5000"] 
}

$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

- net/http: request canceled (Client.Timeout exceeded while awaiting headers)
```sh
$ vim /etc/docker/daemon.json
{
	"registry-mirrors": ["https://ik8akj45.mirror.aliyuncs.com"]
    # "registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]
}
```

## Questions

- spring.cloud.client.ip-address[10.27.213.167/172.16.81.167]
- spring.cloud.client.ip-address[10.27.213.167/172.16.81.167]