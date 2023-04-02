# Ch.04 쿠버네티스 기초

쿠버네티스 클러스터룰 컨트롤 하기 위해서는 kubectl이라는 명령툴을 사용한다. 쿠버넽티스 API 서버는 REST API로 통신을 한다. 사용자가 직접 HTTP프로토콜로 API서버와 통신할수도 있지만 쿠버네티스에서는 쉽게 마스터와 통신할 수 있게 해주는 클라이언트 툴인 kubectl을 제공한다.

## 4.1 기본 명령

컨테이너를 조작하는 docker와 마찬가지로 kubectl명령을 통해 쿠버네티스 클러스터를 조작해본다.

### 4.1.1 컨테이너 실행

<aside>
💡 kubectl run mynginx —image nginx

</aside>

name: mynginx

image: nginx

### 4.1.2 컨테이너 조회

<aside>
💡 kubectl get pod

</aside>

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled.png" width="80%" height="80%"/>

> pod?
Pod는 쿠버네티스의 가장 작은 실행단위
> 

Pod Status

- Pending: 쿠버네티스 마스터에 생성 명령은 전달되었지만 아직 실행되지 않은 상태
- ContainerCreating: 특정 노드에 스케줄링되어 컨테이너를 생성중인 단계(이미지 다운로드 등)
- Running: Pod가 정상적으로 실행되고 있는 상태
- Completed: 계속 실행되고 있는 프로세스가 아닌 한 번 실행되고 완료되는 배치작업 Pod에서 작업이 완료된 상태를 말함
- Error: Pod에 문제가 생겨 에러가 발생
- CrashLoopBackOff: 지속적으로 에러 상태로 있어 crash가 반복되는 상태

kubectl get pod mynginx -o yaml 사용시 Pod의 상태정보를 더 자세히 알 수 있음

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%201.png" width="80%" height="80%"/>

podIP, nodeName 등을 알 수 있음

kubectl get pod -o wide: PodIP를 조회할 수 있음

### 4.1.3 컨테이너 상세정보 확인

<aside>
💡 kubectl describe pod mynginx

</aside>

get명령과 유사하게 pod의 상태정보를 보여줌, get과의 차이는 pod에 대한 이벤트기록까지 확인가능, 문제발생시 get명령과 함께 디버깅 용도로 사용

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%202.png" width="80%" height="80%"/>

### 4.1.4 컨테이너 로깅

<aside>
💡 kubectl logs -f mynginx

</aside>

컨테이너 로그정보 확인, -f(follow)옵션 사용 시 출력스트림이 종료되지 않고 지속적으로 로그출력

kubectl wait —for condition=Ready pod mynginx: pod생성이 완료될 때까지 기다림

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%203.png" width="80%" height="80%"/>

### 4.1.5 컨테이너 명령전달

<aside>
💡 kubectl exec <NAME> — <CMD>
kubectl exec mynginx — apt-get update
kubectl exec -it mynginx — bash

</aside>

-it 옵션 이용시 컨테이너 내부로 진입가능

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%204.png" width="80%" height="80%"/>

### 4.1.6 컨테이너 / 호스트간 파일 복사

<aside>
💡 kubectl cp /etc/passwd mynginx:/tmp/passwd

kubectl exec mynginx — ls /tmp/passwd

kubectl exec mynginx — cat /tmp/passwd

</aside>

host의 passwd파일 mynginx pod의 /tmp/passwd에 복사

exec로 복사확인

cat로 mynginx의 /tmp/passwd파일 내용출력

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%205.png" width="80%" height="80%"/>

### 4.1.7 컨테이너 정보 수정

<aside>
💡 kubectl edit pod mynginx

</aside>

실행된 컨테이너의 정보를 vim을 통해 수정

kubectl get pod mynginx -oyaml: 컨테이너 상세정보 조회

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%206.png" width="80%" height="80%"/>

### 4.1.8 컨테이너 삭제

<aside>
💡 kubectl delete pod mynginx

</aside>

### 4.1.9 선언형 명령 정의서(YAML) 기반 컨테이너 생성

<aside>
💡 kubectl apply -f <FILE_NAME>

</aside>

```yaml
#mynginx.yaml
apiVersioin: v1
kind: Pod
metadata: 
    name: mynginx
spec:
    containers:
    - name: mynginx
    - image: nginx
```

- kubectl apply -f mynginx.yaml
    
    kubectl run 명령과 동일하게 kubectl apply명령을 사용해서도 컨테이너를 생성시킬 수 있다.
    
    run은 명령형 스타일의 명령이고 apply는 선언형 스타일의 명령이다.
    
- kubectl get pod mynginx -oyaml

.yaml파일을 이용하여 컨테이너를 생성할 수 있음

<aside>
💡 kubectl apply -f https://raw.githubusercontent.com/kubernates/website/master/content/en/example/content/en/examples/pod/simple-pod.yaml

kubectl delete -f https://raw.githubusercontent.com/kubernates/website/master/content/en/example/content/en/examples/pod/simple-pod.yaml

</aside>

: 인터넷에 위치한 yaml정의서를 가지고도 컨테이너를 실행, 삭제 시킬 수 있음

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%207.png" width="80%" height="80%"/>

mynginx.yaml에  라벨을 추가하고 다시 실행하면 새로 컨테이너가 생성되는 것이 아닌 컨테이너의 설정값이 바뀌게 된다. 동일한 파일에 대해 똑같이 apply명령을 수행해도 기존의 컨테이너는 변경되지 않는다.

<aside>
💡 멱등성(idempotent): 동일한 연산을 여러번 적용하더라도 최종 결과가 달라지지 않는 성질, 자료구조 set는 멱등성을 보장한다. → 동일한 원소를 여러번 추가해도 최종적으로는 원소가 중복되지 않기 때문이다.

</aside>

## 4.2 고급 명령

### 4.2.1 리소스별 명령

쿠버네티스의 모든것은 리소스로 표현된다. 쿠버네티스의 리소스는 Pod외에 Service, ReplicaSet, Deployment 등 다양한 리소스를 포함하고 있다.

- service, node 리소스 살펴보기
    
    <aside>
    💡 kubectl get service
    kubectl get node
    
    </aside>
    
    ![Untitled](../images/CH04_image/Untitled%208.png)
    
- describe 명령도 동일하게 적용됨
    
    <aside>
    💡 kubectl describe service <service_name>
    kubectl describe service kubernetes
    kubectl describe service node master
    
    </aside>
    
    <img src="https://github.com/Jaemin-        kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%208.png" width="80%" height="80%"/>
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%209.png" width="80%" height="80%"/>
    

### 4.2.2 네임스페이스(Namespace)

네임스페이스는 쿠버네티스 클러스터를 논리적으로 나누는 역할을 한다. Pod, Service와 같은 리소스가 네임스페이스별로 생성되고 네임스페이스별로 사용자 접근제어, Network 접근제어 정책을 다르게 설정할 수 있다.

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%2010.png" width="80%" height="80%"/>

- default: 기본 네임스페이스로 아무옵션 없이 컨테이너를 만들면 default 네임스페이스에 생성된다.
- kube-system: 쿠버네티스의 핵심 컴포넌트들이 있는 네임스페이스로 해당 네임스페이스에 네트워크 설정, DNS 서버 등 중요한 역할을 담당하는 컨테이너가 존재한다
- kube-public: 외부로 공개 가능한 리소스를 담고있는 네임스페이스
- kube-node-lease: 노드가 살아있는지 마스터에 알리는 용도로 존재하는 네임스페이스

명령을 실행 할 때, —namespace옵션(-n)을 이용하여 특정 네임스페이스에 리소스를 생성할 수 있다.

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%2011.png" width="80%" height="80%"/>

네임스페이스 옵션 생략시 default 네임스페이스가 설정된다.

### 4.2.3 자동완성 기능

kubectl명령을 자동완성하게 해주는 쉘스크립트 제공

```bash
echo 'source <(kubectl completion bash)' >> ~/.bashrc
```

### 4.2.4 즉석 리소스 생성

YAML 정의서를 사용하지 않고 cat & here document명령어를 이용하여 빠르게 리소스 생성가능

```bash
cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
    name: cat-nginx
spec:
    containers:
    - image: nginx
      name: cat-nginx
EOF
```

### 4.2.5 리소스 특정 정보 추출

—jsonpath 를 이용하면 리소스의 특정 정보만을 골라서 추출할 수 있음

찾고자하는 값의 json경로를 기록하면 경로에 대한 값이 추출됨

<aside>
💡 kubectl get node master -o jsonpath="{.status.addresses[0].address}”

</aside>

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%2012.png" width="80%" height="80%"/>

마스터 노드 IP출력

### 4.2.6 모든 리소스 조회

<aside>
💡 kubectl api-resources

</aside>

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%2013.png" width="80%" height="80%"/>

쿠버네티스 리소스는 네임스페이스 레벨 리소스와 클러스터 레벨 리소스로 구분됨. 

- 네임스페이스 레벨 리소스는: 해당 리소스가 반드시 특정 네임스페이스에 속해야하는 리소스(ex. Pod)
- 클러스터레벨 리소스: 네임스페이스와 상관 없이 클러스터 레벨에 존재하는 리소스(ex. Node)

네임스페이스 레벨의 API리소스만 탐색하기 위한 명령어는 다음과 같음

<aside>
💡 kubectl api-resources --namespaced=true

</aside>

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%2014.png" width="80%" height="80%"/>

### 4.2.7 리소스 정의 설명

<aside>
💡 kubectl explain pods

</aside>

리소스의 정의와 설명을 살펴보는 커맨드

### 4.2.8 클러스터 상태 확인

<aside>
💡 #API서버 작동여부
kubectl cluster-info

# 전체 노드상태 확인
kubectl get node

# 쿠버네티스 핵심 컴포넌트 Pod상태 확인
kubectl get pod -n kube-system

</aside>

전반적인 cluster health check

### 4.2.9 클라이언트 설정 파일

<aside>
💡 #view명령을 통해 설정 파일 직접 출력
kubectl config view

#KUBECONFIG설정파일 직접 출력
cat $HOME/.kube/config

</aside>

<img src="https://github.com/Jaemin-kr/DevOps/blob/b4a15fb01b209f2cec3658b9e034d3a6c4b5e70a/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/CH04_image/Untitled%2015.png" width="80%" height="80%"/>

KUBECONFIG파일 

- cluster: kubectl 툴이 바라보는 클러스터 정보를 입력, 예제에서는 쿠버네티스 API서버와 kubectl 툴이 동일한 서버에 존재하므로 localhost(127.0.0.1)을 바라보지만 원격에 위치한 클러스터인 경우 원격 주소지를 입력함
- users: k8s 클러스터에 접속하는 사용자정의
- contxext: 클러스터와 user를 연결해주는 것, kubectl이 여러개의 클러스터나 사용자를 관리할 경우 유용하게 사용가능

### 4.2.10 kubectl 명령 치트시트

kubectl 명령어 확인가능

[kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

### Clean up

<aside>
💡 kubectl delete pod —all

</aside>
