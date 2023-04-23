# Ch.06 쿠버네티스 네트워킹

쿠버네티스의 네트워크를 담당하는 Service리소스에 대해 알아본다. Service리소스도 Pod와 마찬가지로 YAML형식으로 정의되며 Service리소스는 Pod IP와는 다른 IP를 부여받아 Endpoint를 제공하며 라벨링 시스템을 통해 Pod로 트래픽을 전달한다. Service리소스는 Pod앞단에 위치하여 마치 로드 밸런서처럼 동작한다.

 

## 6.1 Service 소개

쿠버네티스는 Pod자체에도 IP가 부여되며 curl 명령어를 통해 PodIP로 호출을 통해 정상적으로 결과를 반환함. 해당 IP를 통해 요청을 보내고 응답을 받을 수 있다. 그렇다면 Service리소스를 만들어 네트워크 통신을 담당하게 만든 이유는?

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/Ch06_image/Untitled.png" width="80%" height="80%"/>

### 6.1.1 불안정한 Pod vs 안정적인 Service

쿠버네티스에서 Pod 리소스는 불안정한 자원으로 여긴다. Pod는 쉽게 생성하고 사용이 끝나면 쉽게 삭제가 가능하기 때문이다. 그렇기에 Pod는 언제든지 어떤 이유로든 종료될 수 있는 리소스로 생각한다. 이러한 특징으로 인해 Pod는 불안정한 서비스 Endpoint를 제공한다. 

Pod 리소스에 부여되는 IP를 이용하여 서비스를 호출하는 경우, 사용자가 끊임없이 서비스 Endpoint의 이상여부와 바뀐 IP를 추적해야한다. 이러한 문제를 해결하기 위해 Service라는 리소스를 통해 Pod의 앞단에서 Service로 들어오는 트래픽을 Pod로 전달하는 리버스 프록시의 역할을 수행하며 Pod의 IP가 변경되더라도 사용자의 입장에서는 동일한 IP로 접근할 수 있고 Pod가 죽더라도 Service가 다른 Pod로 트래픽을 전달해 주기 때문에 **안정성** 및 **가용성**을 높일 수 있다.

<Google Cloud><img src="https://github.com/Jaemin-kr/DevOps/blob/main/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/Ch06_image/Untitled%201.png" width="80%" height="80%"/>

<Google Cloud>

<aside>
💡 리버스 프록시(reverse proxy)
클라이언트 서버 구조에서 서버로 전송되는 요청을 대신 받아 원래의 서버로 전달해주는 서버를 위미한다. 서버로 요청되는 부하를 분산시키고 보안을 높이는 용도로 사용된다.

</aside>

### 6.1.2 서비스 탐색(Service Discovery)

Service리소스는 안정적인 IP를 제공하며 서비스 탐색기능을 수행하는 도메인 이름 기반의 서비스 Endpoint를 제공한다. 사용자는 쿠버네티스 클러스터 내에서 Service리소스의 이름을 기반으로 DNS참조가 가능하다. 이를 통해 사용자는 손쉽게 다른 서비스를 탐색하고 참조한다.

ex) myservice라는 이름의 Service리소스를 생성하면 사용자는 myservice라는 도메인 주소로 해당 Service에 요청할 수 있다.

### 6.1.3 Service 리소스 생성

```yaml
#myservice.yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    hello: world
  name: myservice
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 80
  selector:
    run: mynginx
```

- kind: Service리소스 선언
- metadata: 리소스의 메타 정보를 나타냄
- labels: Service에 라벨부여
- name: 이름을 지정, 해당 이름이 도메인 주소로 활용됨
- spec: Service 스펙을 정의
- ports: Service의 포트들을 정의함
- port: Service로 들어오는 포트를 지정함
- protocol: 사용하는 프로토콜을 지정함. TCP, UDP, HTTP 등이 있음
- targetPort: 트래픽을 전달한 컨테이너의 포트지정
- selector: 트래픽을 전달한 컨테이너의 라벨을 선택, 예제는 run=mynginx 라벨을 가진 Pod에 Service트래픽을 전달

```yaml
kubectl apply -f myservice.yaml

kubectl get service

kubectl get pod -owide
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/Ch06_image/Untitled%202.png" width="80%" height="80%"/>

```yaml
kubectl run client --image nginx

#1. Pod IP로 접근
kubectl exec client -- curl 10.42.0.33

#2. Service IP로 접근
kubectl exec client -- curl 10.43.52.73:8080

#3. Service이름 (DNS 주소)로 접근
kubectl exec client -- curl myservice:8080
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/Ch06_image/Untitled%203.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/Ch06_image/Untitled%204.png" width="80%" height="80%"/>

- Pod IP, Service IP, Service이름으로 요청하는 경우 모두 같은 결과 반환
- Service 끝점으로 요청하는 경우 Service에서 사용하는 포트 8080을 사용

myservice IP주소를 확인하기 위해 DNS lookup을 수행

```bash
kubectl exec client -- sh -c "apt update && apt install -y dnsutils"
kubectl exec client -- nslookup myservice
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/%ED%95%B5%EC%8B%AC%EB%A7%8C%20%EC%BD%95!%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/images/Ch06_image/Untitled%205.png" width="80%" height="80%"/>
