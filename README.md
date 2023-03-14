# 핵심만 콕! 쿠버네티스

---

OS: Ubuntu 20.04

권장스펙: CPU4, Mem 16G, Disk50G

쿠버네티스 클러스터: k3s v1.18.6+k31s

helm v3.2.2

[(k8s) M1 맥북으로 클러스터 생성(M1 VirtualMachine)](https://eocis.app/117/)

## 사전지식

---

- 리눅스 기본지식(기본 명령어, ssh, vim, appt, curl, …)
- 기본적인 웹기술에 대한 이해(HTTP, TLS, NGINX, 네트워크, …)
- python

dockerhub

jaemin98

UTM ubuntu 20.04 M1 install

[M1 Mac 에서 Ubuntu 20.04 ARM 설치하는 방법 (GUI 까지)](https://sincerity.page/random/Random-How_to_install_Ubuntu20.04_in_M1/)

### Vmware License

---

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled.png)

## 도커설치

---

AWS EC2 Free-Tier Ubuntu 20.04로진행

sudo apt update && sudo apt install -y [docker.io](http://docker.io/) net-tools

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%201.png)

sudo usermod -aG docker $USER

sudo reboot

sudo apt install -y cowsay

cowsay hello world!

docker run docker/whalesay cowsay 'hello world!’

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%202.png)

docker run -d nginx #nginx 이미지 run

docker ps 프로세스 아이디 확인

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%203.png)

로그확인

docker logs -f 73588180c437

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%204.png)

nginx 컨테이너에 명령 전달

docker exec 73588180c437 sh -c 'apt update && apt install -y wget’

docker exec 73588180c437 wget localhost

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%205.png)

컨테이너/호스트간 파일복사

           호스트             컨테이너

docker cp /etc/passwd 73588180c437:/usr/share/nginx/html/.

확인: docker exec 73588180c437 curl localhost/passwd

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%206.png)

                    컨테이너                                                                 호스트(현재위치)

docker cp 73588180c437:/usr/share/nginx/html/index.html .

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%207.png)

컨테이너 중단/재개/삭제

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%208.png)

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%209.png)

삭제 stop and rm

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%2010.png)

interactive container: 이미지 실행시 -it옵션을 통해 직접 컨테이너 안으로 접속하여 작업할 수 있음

-it는 interactive(stdin, stdout 연결), tty(터미널연결)의 약자

ubuntu 16.04이미지를 실행하며 bash 쉘을 실행

docker run -it ubuntu:16.04 bash

nginx 이미지를 실행하고 exec명령을 통해 bash접속

컨테이너 실행

docker run -d nginx bash

bash 접속

docker exec -it [Container ID] bash

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%2011.png)

UTM ubuntu 20.04로 진행

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%2012.png)

docker hub에 이미지 push

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%2013.png)

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%2014.png)

hello.py

```python
import os
import sys

my_ver = os.environ["my_ver"]
arg = sys.argv[1]

print("hello %s, my version is %s!" % (arg, my_ver))
```

Dockerfile

```docker
FROM ubuntu:18.04

RUN apt-get update \
	&& apt-get install -y \
	  curl \
	  python-dev

ARG my_ver=1.0

WORKDIR /home/jaemin
COPY hello.py .
ENV my_ver $my_ver

CMD ["python", "hello.py", "guest"]
```

![Untitled](%E1%84%92%E1%85%A2%E1%86%A8%E1%84%89%E1%85%B5%E1%86%B7%E1%84%86%E1%85%A1%E1%86%AB%20%E1%84%8F%E1%85%A9%E1%86%A8!%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20a3cc7d95c454417ba2fc5785fd14e515/Untitled%2015.png)
