# Ch.03 쿠버네티스 설치

마스터 노드, 워커노드

사용자 이름: jaemin

## 3.1 k3s 소개

## 3.2 k3s 설치

k3s 클러스터 구성: 단일 마스터, 노드 생성

- 마스터와 워커노드의 양방향통신
- 워커노드 접속을 제외하고 모두 마스터노드에서 진행

### 3.2.2 마스터 노드설치

| 프로토콜 | 포트범위 | 목적 |
| --- | --- | --- |
| TCP | 6443 | API 서버 |
| TCP | 2379-2380 | etcd 서버 client API |
| TCP | 10250 | kubelet API |
| TCP | 10251 | kube-scheduler |
| TCP | 10252 | kube-controller-manager |
| TCP | 30000~32767 | nodePort services |

### 포트관련설정

[Ubuntu 20.04 서버에 K3s 클러스터 구성하기](https://cigiko.cafe24.com/ubuntu-20-04-서버에-k3s-클러스터-구성하기/)

열려있는 포트확인 netstat -nap

LISTEN중인 포트확인: netstat -nap | grep LISTEN

특정 포트번호 상태확인: netstat -nap | grep 포트번호

특정포트 open

- TCP
    
    iptables -l INPUT 1 -p tcp —dport 12345 -j ACCEPT
    
    12345포트를 인바운드로 받아들이는 1번 방화벽 규칙으로 추가
    
- UDP
    
    iptables -l INPUT 1 -p udp —dport 12345 -j ACCEPT
    

![Untitled](Ch%2003%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20054d9dc39f3b4b7aa79f4d457be088db/Untitled.png)

포트설정 저장

sudo netfilter-persistent save

sudo apt update

sudo apt install -y [docker.io](http://docker.io) nfs-common dnsutils curl

![Untitled](Ch%2003%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20054d9dc39f3b4b7aa79f4d457be088db/Untitled%201.png)

마스터노드 생성 및 상태확인

![Untitled](Ch%2003%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20054d9dc39f3b4b7aa79f4d457be088db/Untitled%202.png)

토큰ID:

K109cc94b6bcdb04052435fbbb9c5649b078845dd06e40d59a6d0352feb782be5f1::server:79cab0c12fa4563f3ecb0747ee88c2bc

IP: 192.168.64.5

워커노드

IP:192.168.64.6

우분투 서버 초기실행시 ssh 연결 refuse될 때

[Ubuntu SSH Connection 연결 실패 혹은 Refused/Fail](https://itpground.tistory.com/6)

1. Ubuntu에 ssh가 설치되어있지 않음
    - sudo apt update
    - sudo apt upgrate
    - sudo apt install openssh-server
    - sudo apt install ssh
    - sudo service ssh start
    
    service ssh status로 ssh상태 확인가능
    
    ![Untitled](Ch%2003%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20054d9dc39f3b4b7aa79f4d457be088db/Untitled%203.png)
    

워커노드에 k3s 설치

> curl -sfL [https://get.k3s.io](https://get.k3s.io/) | K3S_URL=https://$MASTER_IP:6443 \
K3S_TOKEN=$NODE_TOKEN \
INSTALL_K3S_EXEC="--node-name worker --docker" \
INSTALL_K3S_VERSION="v1.18.6+k3s1" sh -s -
> 

마스터 노드에서 상태확인

kubectl get node -o wide

![Untitled](Ch%2003%20%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%82%E1%85%A6%E1%84%90%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20054d9dc39f3b4b7aa79f4d457be088db/Untitled%204.png)

INTERNAL-IP가 각서버의 내부 IP주소

노드 삭제작업

- /usr/local/bin/k3s-uninstall.sh
- /usr/local/bin/k3s-agent-uninstall.sh