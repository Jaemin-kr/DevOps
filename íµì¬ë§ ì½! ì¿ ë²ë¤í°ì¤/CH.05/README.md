# Ch.05 Pod 살펴보기

Pod는 쿠버네티스의 가장 기본리소스로 쿠버네티스에서는 각 리소스들이 빌딩 블럭처럼 동작하며 여러개의 빌딩블록을 조합하여 또 다른 빌딩블럭을 만들어낸다.

## 5.1 Pod소개

쿠버네티스의 최소 실행단위: Pod, 아무리 작고 단순한 프로세스라도 Pod를 통해 실행되며 쿠버네티스는 Pod를 통해 기본 가상환경을 제공한다. Pod는 가상머신의 instance, 도커의 컨테이너와 같다.

**가상플랫폼 실행단위**

- 가상머신: Instance
- 도커: Container
- 쿠버네티스: Pod

### 5.1.1 Pod 특징

<aside>
💡 NAT(Network Address Translation)
: 여러개의 내부 IP를 1개의 외부 IP로 연결하고 내부 기기들을 포트로 구분

</aside>

- 1개 이상의 컨테이너 실행
: 보통 1개의 컨테이너를 실행하지만 이론상 3개 이상의 컨테이너도 가능
- 동일 노드에 할당
: Pod내에서 실행되는 컨테이너는 모두 동일노드에 할당되며 동일한 생명주기를 가진다.(Pod삭제시 Pod내의 모든 컨테이너 삭제)
- 고유의 Pod IP
: Pod리소스는 노드 IP와 별개로 클러스터 내에서 접근 가능한 고유의 IP를 할당받음. 도커 컨테이너의 경우 다른 노드에 위치한 컨테이너간의 통신을 위해 일반적으로 포트포워딩을 이용하여 노드 IP와 포워딩 포트를 이용하여 접근. 쿠버네티스에서는 다른 노드에 위치한 Pod라 하더라고 NAT통신 없이 Pod고유의 IP를 이용하여 접근할 수 있음
- IP 공유
: Pod내의 컨테이너들은 IP를 공유하며 localhost를 통해 서로 접근이 가능하기에 포트번호를 이용하여 구분
- volume 공유
: Pod안의 컨테이너들은 동일한 볼륨과 연결가능하며 파일시스템을 기반으로 파일을 주고받을 수 있음

Pod YAML을 이용하여 명령 정의서 만들기

—dry-run, -o yaml옵션 이용시 Pod를 생성하지 않고 템플릿 파일만 생성할 수 있음

```bash
kubectl run mynginx --image nginx --dry-run=client -o yaml > mynginx.yaml

vim mynginx.vim
```

```yaml
#mynginx yaml
apiVersion: v1
kind: Pod
metadata:
	labels:
		run: mynginx
	name: mynginx
spec:
	containers:
	- image: nginx
	 name: mynginx
	restartPolicy: Never
```

- apiVersion: 모든 리소스에는 apiVersion이 정의되어 있음, 리소스의 이름이 동일할 경우 이름 충돌을 피하기 위한 목적으로 리소스의 scope을 정의한것으로 프로그래밍 언어의 패키지와 비슷함
- kind: 리소스의 타입을 정의, 클래스와 비슷한 역할을 함
- metadata: 리소스의 메타정보를 나타냄
    - labels: 리소스의 라벨정보를 표기
    - name: 리소스의 이름을 표기함(mynginx)
- spec: 리소스의 스펙정의
    - containers: 1개이상의 컨테이너를 정의
        - name: 컨테이너 이름표기
        - image: 컨테이너 이미지 주소를 지정

YAML을 이용한 Pod 생성

```bash
kubectl apply -f mynginx.yaml
```

Pod 생성과정

1. User가 kubectl명령을 통해 Pod 정의를 쿠버네티스 마스터에 전달
2. 마스터는 YAML정의의 유효성 체크 후 특정 노드에 사용자 요청에 따라 컨테이너를 실행하도록 명령
3. 명령을 전달받은 노드(kubectl)는 요청사항에 맞게 실제 컨테이너를 노드에 실행

## 5.2 라벨링 시스템

중요 매커니즘, 특정 리소스 참조, Pod에 트래팍 전달시에 라벨링 시스템을 활용한다. 라벨은 단순히 key, value형태의 문자열로 Pod에 라벨을 부여한다는 의미는 Pod의 metadata property에 key, value형태의 문자열을 추가한다는 것을 의미한다.

### 5.2.1 라벨 정보부여 방법

- label 명령을 이용하는 방법
    
    ```bash
    kubectl label pod <NAME> <KEY>=<VALUE>
    ```
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/67744136-56ce-4da2-ae81-43dfa7a5a98b/Untitled.png)
    
- 선언형 명령 이용
    
    ```bash
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
    	labels:
    		hello: world
    		run: mynginx
    	name: mynginx
    spec:
    	containers:
    	- image: nginx
    	 name: mynginx
    EOF
    ```
    
    - metadata: 리소스의 메타데이터 정보를 나타냄(리소스 이름, 라벨 등)
    - labels: 라벨 정보를 태깅하기 위해 labels property를 사용

### 5.2.2 라벨정보 확인

```bash
kubectl get pod mynginx -L run
kubectl get pod mynginx --show-labels
```

Pod에 부여된 라벨을 표시하기 위해 -L옵션을 사용, 특정 라벨이 아닌 전체 라벨을 확인하고 싶다면 —show-labels옵션 사용.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/af873852-2eb4-47d5-9349-59162f3a3e59/Untitled.png)

### 5.2.3 라벨을 이용한 조건 필터링

```bash
kubectl run yournginx --image nginx

kubectl get pod -l run
kubectl get pod -l run=mynginx
kubectl get pod -l run=yournginx
```

특정 라벨을 가진 Pod만 보기위해서는 -l옵션이용, 특정 key를 기준으로 할수도 있으며, key와 value를 이용하는것도 가능

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/68bf1930-991e-4580-9107-c84910e1c2db/Untitled.png)

### 5.2.4 nodeSelector를 이용한 노드선택

라벨링 시스템을 통해 Pod가 특정 노드에 할당되도록 스케줄링할 수 있음. 기본 옵션은 쿠버네티스가 판단.

특정노드를 명시적으로 선택해야하는경우 사용

ex) A노드가 SSD, B노드가 HDD로 설정하고, 특정 Pod는 SSD를 사용해야 하는 경우 nodeSelector의 property를 이용하여 노드선택

```bash
kubectl get node --show-labels
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7856918d-cb66-422a-81f6-f1d1ec35a0b7/Untitled.png)

```bash
kubectl label node master disktype=ssd
kubectl label node worker disktype=hdd
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/388bf5b8-4932-43bd-b354-95dc37e6f3ae/Untitled.png)

노드 라벨확인

```bash
kubetl get node --show-labels | grep disktype
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9e0a394d-f86a-4abe-8070-75a4c2a24aa3/Untitled.png)

Pod의 YAML정의서에 nodeSelector property추가

```yaml
#node-selector.yaml
apiVersion: v1
kind: Pod
metadata:
	name: node-selector
spec:
	containers:
	- name nginx
	 image: nginx
#특정노드 라벨선택
nodeSelector:
	disktype:ssd
```

```bash
kubectl apply -f node-selector.yaml
kubectl get pod node-selector -o wide
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/78d49d38-a8a5-4e92-a010-f93f2ccb0eda/Untitled.png)

<aside>
💡 2개 이상의 노드에 동일 라벨이 부여되어 있다면, 쿠버네티스가 노드의 상태를 확인 후 최적의 노드에 배치

</aside>

nodeSelector변경

```yaml
#node-selector.yaml
apiVersion: v1
kind: Pod
metadata:
	name: node-selector
spec:
	containers:
	- name nginx
	 image: nginx
#특정노드 라벨선택
node Selector:
	disktype:hdd
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dc7232e3-84ae-4e3a-9586-59413a70750f/Untitled.png)

```bash
#기존 Pod삭제
kubectl delete pod node-selector
#새로 라벨링한 Pod생성
kubectl apply -f node-selector.yaml

kubectl get pod node-selector -o wide
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0641aeea-94e1-4461-b3d4-2b1c5847870e/Untitled.png)

## 5.3 실행 명령 및 파라미터 지정

Pod 생성 시 실행 명령과 파라미터를 전달할 수 있음

```yaml
#cmd.yaml
apiVersion: v1
kind: Pod
metadata:
	name: cmd
spec:
	restartPolicy: OnFailure
	containers:
	- name: nginx
	  image: nginx
	  #실행 명령
		command: ["/bin/echo"]
		#파라미터
		args: ["hello"]
```

- command: 컨테이너 시작 실행명령 지정, 도커의 ENTRYPOINT에 대응되는 property
- args: 실행 명령에 넘겨줄 파라미터를 지정, 도커의 CMD에 대응되는 property
- restartPolicy: Pod의 재시작 정책을 설정
    - Always: Pod종료시 항상 재시작(default옵션)
    - Never: 재시작을 시도하지 않음
    - OnFailure: 실패시에만 재시작시도

<aside>
💡 YAML파일 작성시 탭(tab)쓰지 않기!

</aside>

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f41df79b-61e5-4481-baf3-afb48c2894f0/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c617d469-461e-4fa1-8132-547679ba5aae/Untitled.png)

nginx로 실행되는 Pod를 hello메시지를 출력하는 쉘 스크립트로 변경함

## 5.4 환경변수 설정

Pod에 환경변수를 전달하는 방법: env property사용

```yaml
#env.yaml
apiVersion: v1
kind: Pod
metadata:
	name: env
spec:
	containers:
	- name: nginx
	  image: nginx
	  env:
		- name: hello
			value: "world!"
```

```bash
kubectl apply -f env.yaml

kubectl exec env -- printenv | grep hello
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6155afec-d12f-4b9c-9adb-34b6b121bdd7/Untitled.png)

## 5.5 볼륨 연결

Pod 내부 스토리지의 생명주기는 Pod와 동일하게 Pod가 사라지면 저장된 데이터도 삭제된다. Pod내에 생성된 데이터를 생명주기와 관계없이 지속적으로 저장하기 위해서는 따로 Volume을 연결해주어야 한다.

쿠버네티스에는 다양한 볼륨타입이 존재하지만 예제에서는 가장 기본이되는 host Volume을 사용한다. host Volume은 도커의 -v 옵션과 유사하게 host 서버의 볼륨공간에 Pod가 데이터를 저장하는 것을 말한다.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: volume
spec:
    containers:
    - name: nginx
      image: nginx

      volumeMounts:
      - mountPath: /container-volume
        name: my-volume

    volumes:
    - name: my-volume
      hostPath:
        path: /home
```

- volumeMounts: 컨테이너 내부에 사용될 볼륨 선언
    - mountPath: 컨테이너 내부에 볼륨이 연결될 위치 지정(/container-volume)
    - name: volumeMount와 volumes을 연결하는 식별자로 사용됨(my-volume)
- volumes: Pod에서 사용할 volume지정
    - name: volumeMount와 volumes을 연결하는 식별자로 사용됨(my-volume)
    - hostPath: 호스트서버의 연결위치 지정(/home)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/02d06229-f774-4fae-8506-63ce58a1db38/Untitled.png)

호스트 서버의 디렉터리를 연결하는 hostPath외에 Pod 내에 임시로 생성하는 emptyDir property도 존재함. emptyDir volume은 주로 컨테이너끼리 파일 데이터를 주고받을 때 자주 사용함

```yaml
# volume-empty.yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-empty
spec:
  containers: 
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: /container-volume
      name: my-volume
  volumes:
  - name: my-volume
    emptyDir: {}
```