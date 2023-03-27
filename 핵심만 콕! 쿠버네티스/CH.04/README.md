# Ch.03 쿠버네티스 기초

쿠버네티스 클러스터룰 컨트롤 하기 위해서는 kubectl이라는 명령툴을 사용한다. 쿠버넽티스 API 서버는 REST API로 통신을 한다. 사용자가 직접 HTTP프로토콜로 API서버와 통신할수도 있지만 쿠버네티스에서는 쉽게 마스터와 통신할 수 있게 해주는 클라이언트 툴인 kubectl을 제공한다.

사용자 이름: jaemin

## 4.1 기본명령

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

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fd08609f-8bca-4509-9d23-781e7a328648/Untitled.png)

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

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/88f68830-8f62-48bf-addf-ed15ede869fa/Untitled.png)

podIP, nodeName 등을 알 수 있음

kubectl get pod -o wide: PodIP를 조회할 수 있음

### 4.1.3 컨테이너 상세정보 확인

<aside>
💡 kubectl describe pod mynginx

</aside>

get명령과 유사하게 pod의 상태정보를 보여줌, get과의 차이는 pod에 대한 이벤트기록까지 확인가능, 문제발생시 get명령과 함께 디버깅 용도로 사용

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c792dc6c-ef48-4f80-adb4-d1fd2b0fa67c/Untitled.png)

### 4.1.4 컨테이너 로깅

<aside>
💡 kubectl logs -f mynginx

</aside>

컨테이너 로그정보 확인, -f(follow)옵션 사용 시 출력스트림이 종료되지 않고 지속적으로 로그출력

kubectl wait —for condition=Ready pod mynginx: pod생성이 완료될 때까지 기다림

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0733c682-c34d-4585-90dd-0457ec628ee8/Untitled.png)

### 4.1.5 컨테이너 명령전달

<aside>
💡 kubectl exec <NAME> — <CMD>
kubectl exec mynginx — apt-get update
kubectl exec -it mynginx — bash

</aside>

-it 옵션 이용시 컨테이너 내부로 진입가능

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bbb27b6d-2469-4c0a-82c2-bd1b5db6fb8b/Untitled.png)

### 4.1.6 컨테이너 / 호스트간 파일 복사

<aside>
💡 kubectl cp /etc/passwd mynginx:/tmp/passwd

kubectl exec mynginx — ls /tmp/passwd

kubectl exec mynginx — cat /tmp/passwd

</aside>

host의 passwd파일 mynginx pod의 /tmp/passwd에 복사

exec로 복사확인

cat로 mynginx의 /tmp/passwd파일 내용출력

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7d0e1e05-34c9-489b-9a90-796885e4f9e0/Untitled.png)

### 4.1.7 컨테이너 정보 수정

<aside>
💡 kubectl edit pod mynginx

</aside>

실행된 컨테이너의 정보를 vim을 통해 수정

kubectl get pod mynginx -oyaml: 컨테이너 상세정보 조회

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e9960ae0-75d6-42d1-b821-ff153e762073/Untitled.png)

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

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c8e6d729-b54b-41c9-95f2-4b1af9a5c812/Untitled.png)

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
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ae37aa30-e88b-456f-8d4c-9d29feb0e10d/Untitled.png)
    
- describe 명령도 동일하게 적용됨
    
    <aside>
    💡 kubectl describe service <service_name>
    kubectl describe service kubernetes
    kubectl describe service node master
    
    </aside>
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd8666fd-d309-4aaf-817b-c09da3be5748/Untitled.png)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/885113a0-40e0-4bc1-81f7-b044cf46814b/Untitled.png)
    

### 4.2.2 네임스페이스(Namespace)

네임스페이스는 쿠버네티스 클러스터를 논리적으로 나누는 역할을 한다. Pod, Service와 같은 리소스가 네임스페이스별로 생성되고 네임스페이스별로 사용자 접근제어, Network 접근제어 정책을 다르게 설정할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0c6bb90f-8e98-48bb-9117-8aa69bd0bbaa/Untitled.png)

- default: 기본 네임스페이스로 아무옵션 없이 컨테이너를 만들면 default 네임스페이스에 생성된다.
- kube-system: 쿠버네티스의 핵심 컴포넌트들이 있는 네임스페이스로 해당 네임스페이스에 네트워크 설정, DNS 서버 등 중요한 역할을 담당하는 컨테이너가 존재한다
- kube-public: 외부로 공개 가능한 리소스를 담고있는 네임스페이스
- kube-node-lease: 노드가 살아있는지 마스터에 알리는 용도로 존재하는 네임스페이스
