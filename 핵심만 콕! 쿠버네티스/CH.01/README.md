# Ch01. 도커 기초

---

## 1.1 도커 소개

---

Docker: 가상실행 환경을 제공해주는 오픈소스 플랫폼으로 가상실행환경을 컨테이너라고 부른다. 도커는 온프레미스환경이든 클라우드 환경이든 상관없이 동일하게 동작하므로 빠르게 인프라 구성이 가능하다.

Container: 도커에서 제공하는 가상실행환경

- 표준화(Standard): 도커는 프로세스의 실행을 표준화시킬 수 있다. 동일한 형식으로 프로세스를 실행하고 관리
- 이식성(Portability): 프로그램이 어디서 만들어지든 상관없이 도커 플랫폼 위에서 실행된다면 동일한 실행환경으로 프로세스를 작동시킬 수 있다.
- 가볍다(Lightweight): 도커는 실행되는 애플리케이션별로 커널을 공유해서 다른 가상화제품에 비해 가볍다.
- 보안성(Secure): 컨테이너라는 고립된 환경에서 실행되므로 보안측면에서 유리함

### 1.1.1 컨테이너와 가상머신

---

기존의 가상환경기술과 도커의 가상실행환경을 비교해보자

컨테이너와 가상머신은 리소스를 가상화하고 고립시키는 측면에서는 동일하지만 실제 동작방식에서 차이가 있다.

컨테이너는 애플리케이션 레벨에서 필요한 소스코드와 라이브러리들을 하나로 패키징하여 별도의 실행환경을 구성한것으로 여러개의 컨테이너가 동일한 호스트에서 커널을 공유하며 실행됨, 커널을 공유하지만 개별적인 **‘사용자공간(user space)’**를 가진다. 컨테이너는 커널을 가지지 않으므로 더 가볍고 실행할 때 더 적은 리소스를 사용한다.

가상머신은 물리적인 하드웨어를 가상화하는 기술, 1개의 서버를 여러개의 서버처럼활용하는 효과를 가짐(UTM, VMware). 개별 VM은 독립적인 운영체제를 포함하기때문에 도커에 비해 고립성은 더 좋을 수 있지만 가상환경 시작에 걸리는 오버해드가 커져서 더무겁고 느리다는 단점이 있음

### 가상머신과 컨테이너의 차이점

- 가상머신: 기존의 가상머신은 기존의 서버에 하이퍼바이저를 설치하고 그 위에 가상 OS와 APP을 패키징한 VM을 만들어 실행하는 방식인 하드웨어 레벨의 가상화를 지원한다.
- 컨테이너: OS를 제외한 나머지 애플리케이션 실행에 필요한 모든 파일을 패키징한다는 점에서 OS레벨의 가상화를 지원

**가상머신(VM)**

| VM
(App - Win) | VM
(App - Ubuntu) |
| --- | --- |
| Hypervisor |  |
| OS |  |
| Infra |  |

**컨테이너(Container)**

| Container | Container |
| --- | --- |
| OS |  |
| Infra |  |

컨테이너는 게스트OS와 하이퍼바이저가 없기 때문에 이로인한 오버헤드를 줄여 훨씬 더 가볍게 프로세스를 실행할 수 있고 컨테이너에 대한 복제와 배포가 더 용이하다.

### 용어정리

<aside>
💡 온프레미스
: 기업이 데이터 센서를 보유하고 시스템 구축에서부터 운영까지를 모두 수행하는 방식, HW, network, OS, middleware 등 시스템 기반의 구성요소를 시스템 요구사항에 맞게 자체적으로 인프라를 구축. 온 프레미스는 클라우드 업체가 보장하는 것 이상의 기밀성이 중요한 데이터를 다루는 시스템에 적함함. 반면, 클라우드는 데이터센터를 보유하지 않아서 탄력적 인프라 구성이 가능하고 서버나 네트워크 등 초기 시스템 투자에 드는 비용이 적은 대신 시간이 지날수록 온프레미스에 비해 유지비용이 많이 발생하므로 클라우드는 트래픽의 변동성이 큰 시스템이나 빠르게 서비스를 런칭하고자 하는 신규시스템에 적합함

하이퍼바이저
: 하드웨어와 가상머신들 사이에서 하드웨어를 가상화하기 위해 하드웨어들을 제어할 뿐만 아니라 각각의 가상머신들을 관리하는 역할을 수행. 하드웨어의 물리적인 리소스를 VM들에게 제공하고 하드웨어간의 I/O명령을 처리하며 VM이 동작할 수 있는 환경제공

</aside>

### 1.1.3 도커설치

---

AWS EC2 Free-Tier Ubuntu 20.04로진행

sudo apt update && sudo apt install -y [docker.io](http://docker.io/) net-tools

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled.png)

sudo usermod -aG docker $USER

sudo reboot

sudo apt install -y cowsay

cowsay hello world!

docker run docker/whalesay cowsay 'hello world!’

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%201.png)

docker run -d nginx #nginx 이미지 run

docker ps 프로세스 아이디 확인

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%202.png)

로그확인

docker logs -f 73588180c437

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%203.png)

nginx 컨테이너에 명령 전달

docker exec 73588180c437 sh -c 'apt update && apt install -y wget’

docker exec 73588180c437 wget localhost

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%204.png)

컨테이너/호스트간 파일복사

           호스트             컨테이너

docker cp /etc/passwd 73588180c437:/usr/share/nginx/html/.

확인: docker exec 73588180c437 curl localhost/passwd

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%205.png)

                    컨테이너                                                                 호스트(현재위치)

docker cp 73588180c437:/usr/share/nginx/html/index.html .

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%206.png)

컨테이너 중단/재개/삭제

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%207.png)

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%208.png)

삭제 stop and rm

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%209.png)

interactive container: 이미지 실행시 -it옵션을 통해 직접 컨테이너 안으로 접속하여 작업할 수 있음

-it는 interactive(stdin, stdout 연결), tty(터미널연결)의 약자

ubuntu 16.04이미지를 실행하며 bash 쉘을 실행

docker run -it ubuntu:16.04 bash

nginx 이미지를 실행하고 exec명령을 통해 bash접속

컨테이너 실행

docker run -d nginx bash

bash 접속

docker exec -it [Container ID] bash

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2010.png)

UTM ubuntu 20.04로 진행

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2011.png)

docker hub에 이미지 push

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2012.png)

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2013.png)

### 1.4.1 Dockerfile 기초

---

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

- FROM: base image를 나타냄 해당이미지를 기반으로 새로운 도커 이미지 생성
- RUN: 사용자가 지정한 명령을 실행하는 지시자
- WORKDIR: 이미지의 작업폴더를 지정, /~ 이용
- COPY: 로컬 호스트에 존재하는 파일을 이미지 안으로 복사
- ENV: 이미지의 환경변수 지정
- CMD: 이미지 실행시 default 실행 명령을 지정

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2014.png)

## 1.5 도커 실행 고급

---

### 1.5.1 Network

```bash
docker run -p 5000:80 -d nginx
```

외부의 트래픽을 컨테이너 내부로 전달하기위해 로컬 호스트 서버와 컨테이너의 포트를 매핑시켜 트래픽 포워딩(포트 포워딩) 호스트의 5000번 포트를 컨테이너의 80번 포트로 매핑

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2015.png)

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2016.png)

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2017.png)

### 1.5.2 Volume

```bash
docker run -p 6000:80 -v $(pwd):/usr/share/nginx/heml/ -d nginx
```

컨테이너는 휘발성 프로세스이므로 컨테이너 내부의 데이터를 영구적으로 저장하려면 로컬호스트의 파일시스템을 컨테이너와 연결해야 함(볼륨마운트)

pwd를 컨테이너의 nginx 디렉터리와 연결

변경사항이 많을경우 컨테이너 내부에 파일을 두지 않고 이를 호스트 서버로 옮겨 더 손쉽게 파일을 수정가능, 컨테이너 종료시 데이터가 사라지지 않으며 데이터가 유지됨

### 1.5.3 Entrypoint

ENTRYPOINT는 파라미터 전달 시, override되지 않지만 —entrypoint옵션으로 ENTRYPOINT를 강제로 override하는 방법이 있다.

```bash
#Dockerfile
FROM ubuntu:18.04

ENTRYPOINT ["echo"]
```

```bash
docker build . -t lets-echo
docker run lets-echo hello
#hello

#cat의 결과가 출력되는것을 기대하나 cat '/etc/passwd'문자열이 출력됨
docker run lets-echo cat /etc/password
#cat /etc/password

#entrypoint를 cat 명령으로 override
docker run --entrypoint=cat lets-echo /etc/passwd
#passwd파일 내용 출력됨
```

### 1.5.4 User

기본적으로 컨테이너의 유저는 root지만 보안상의 이유로 일반유저를 만들어 사용할 수 있음

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2018.png)

```bash
#Dockerfile
FROM ubuntu:18.04

RUN adduser --disabled-password --gecos "" ubuntu

#컨테이너 실행시 ubuntu(일반유저)로 실행
USER ubuntu

#my-user이미지 생성
docker build . -t my-user

#ubuntu라는 유저로 컨테이너 실행
docker run -it my-user bash
ubuntu@~:/$

```

ubuntu유저로 컨테이너 실행 후 apt update → permission denied)

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2019.png)

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2020.png)

root user로 컨테이너 실행 후 apt update

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2021.png)

컨테이너는 가상환경을 통해 도커가 설치된 어디서든 정상적으로 프로세스가 동작하게 해준다. 쿠버네티스는 이러한 도커의 특성을 이용하여 여러 서버에서 여러개의 컨테이너를 관리하는데 집중한다.

### Clean up

```bash
#생성된 모든 컨테이너 삭제
docker rm $(docker ps -aq) -f
docker rmi $(docker images -q) -f
```

![Untitled](Ch01%20%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%20ee3d99e02fa44966853440e938907a95/Untitled%2022.png)