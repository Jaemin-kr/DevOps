# AWS CI/CD Workshop

## AWS CI/CD Workshop

[Workshop Studio](https://catalog.us-east-1.prod.workshops.aws/workshops/ef1c179d-8097-4f34-8dc3-0e9eb381b6eb/en-US/10-setup/200-create-ide)

## AWS 설정

- Amazon Elastic Compute Cloud
- Amazon Simple Storage Service
- AWS Identity and Access Management
- AWS Lambda
- AWS Cloud9
- AWS CodeCommit
- AWS CodeBuild
- AWS CodePipelines
- AWS CodeDeploy

**도쿄리전**에서 진행

## Cloud9 IDE

AWS Cloud9은 IDE로 브라우저에서 코드를 작성하고 디버깅할 수 있는 환경을 제공함, code editor, debugger, and terminal을 제공

Cloud9 enviroment create

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%201.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%202.png" width="80%" height="80%"/>

사용자 로그인정보 확인

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%203.png" width="80%" height="80%"/>

## Java Workshop

### Infrastructure

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%204.png" width="80%" height="80%"/>

준비사항

- CDK Application
- CDK Bootstrap
- CDK Deploy

### Prepare CDK Application

Cloud9에서 CDK 애플리케이션 소스를 다운로드

```bash
curl -s https://static.us-east-1.prod.workshops.aws/public/8c4076ba-a416-424d-acc7-06e5cc2de102//static/20-infrastructure/InfrastructureApp.tgz | tar -xzv
cd InfrastructureApp
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%205.png" width="80%" height="80%"/>

CDK 애플리케이션 종속성 설치 및 CDK 프로젝트 빌드

```bash
npm install
npm run build
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%206.png" width="80%" height="80%"/>

### CDK Bootstrap

Bootstrap the CDK into your account and region. This is a one-time operation that creates the resources necessary to allow CDK deployments in your AWS account to a given region.

```bash
cdk bootstrap
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%207.png" width="80%" height="80%"/>

### CDK Deploy

계정 및 지역에 CDK 애플리케이션 배포

```bash
cd ~/environment/InfrastructureApp
cdk deploy
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%208.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%209.png" width="80%" height="80%"/>

```html
Outputs:
InfrastructureStack.BucketName = infrastructurestack-artifactbucket7410c9ef-c0ico2jsqoo0
InfrastructureStack.BuildRoleArn = arn:aws:iam::***
InfrastructureStack.DeployRoleArn = arn:aws:iam::***
InfrastructureStack.DevLocation = http://ec2-18-183-120-10.ap-northeast-1.compute.amazonaws.com
InfrastructureStack.PrdLocation = http://ec2-13-231-79-173.ap-northeast-1.compute.amazonaws.com
Stack ARN:
arn:aws:cloudformation:***
```

### Repository

## Repository

인프라 배포 이후 CI/CD 파이프라인에 필요한 구성 요소 구축, 소스 코트 저장소부터 설정

- Create Repository
- Seed Repository

### Create Repository

- AWS CodeCommit으로 이동
- Create repository
- name: DemoApp
- Create

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2010.png" width="80%" height="80%"/>

URL복제 → HTTPS 복제: 리포지토리 URL을 복사

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2011.png" width="80%" height="80%"/>

[https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/DemoApp](https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/DemoApp)

### Seed Repository

데모 자바 웹 애플리케이션으로 비어있는 새 Git Repository를 시드함

enviroment폴더로 이동후 깃 리포지토리 복제

```bash
cd ~/environment
git clone <DemoApp repository URL>
```

비어있는 새 리포지토리에 자바 애플리케이션 설치

```bash
curl -s https://static.us-east-1.prod.workshops.aws/public/8c4076ba-a416-424d-acc7-06e5cc2de102//static/30-source/DemoApp.tgz | tar -xzv
cd DemoApp
```

초기커밋 전 이메일 및 이름 설정 후 변경사항 commit and push

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"

git add .
git commit -m "Initial commit"
git push
```

## Build

배포하기 이전에 애플리케이션을 배포가능한 아티팩트로 빌드해야 함, Java 웹 애플리케이션의 경우 아티팩트는 WAR파일이다.

- Create Build Project
- Add Build Specification
- Verify Build

### Create Build Project

- AWS CodeBuild로 이동
- Create build project
- Project Name: DemoApp
- Source
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2012.png" width="80%" height="80%"/>
    
- Environment
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2013.png" width="80%" height="80%"/>
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2014.png" width="80%" height="80%"/>
    
- Buildspec 및 Artifacts
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2015.png" width="80%" height="80%"/>
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2016.png" width="80%" height="80%"/>
    
- Log: Default

### Add Build Specification

Cloud9에서 DemoApp폴더에 buildspec.yml파일 생성

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto8
  build:
    commands:
      - mvn install
artifacts:
  files:
    - target/javawebdemo.war
    - appspec.yml
    - scripts/**/*
  discard-paths: no
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2017.png" width="80%" height="80%"/>

변경사항 push

```bash
cd ~/environment/DemoApp
git add .
git commit -m "Add buildspec"
git push
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2018.png" width="80%" height="80%"/>

### 빌드확인

- AWS CodeBuild로 이동
- DemoApp선택
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2019.png" width="80%" height="80%"/>
    
- start build
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2020.png" width="80%" height="80%"/>
    
- 빌트 상태 확인
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2021.png" width="80%" height="80%"/>
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2022.png" width="80%" height="80%"/>
    

## Deploy

WAR파일이 생성되었으므로 테스트 네트워크의 EC2 인스턴스에서 실행되는 서버에 애플리케이션 배포가 가능함

### Create Application and Deployment Group

AWS CodeDeploy → Create Application

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2023.png" width="80%" height="80%"/>

DemoApp선택 후 배포그룹 생성

- 배포그룹 이름: Development
- 서비스 역할(IAM 선택)
- 배포 유형: 애플리케이션 배포방법 → 현재 위치
- 환경구성
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2024.png" width="80%" height="80%"/>
    
- 배포설정: CodeDeployDefault.AllAtOnce
- 로드밸런서: 로드밸런싱 활성화 체크해제
    
    <img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2025.png" width="80%" height="80%"/>
    
- 배포그룹 생성

### Add Application Specification

Cloud9 환경의 DemoApp에 appspec.yml 파일 생성

```yaml
#appspec.yml
version: 0.0
os: linux
files:
  - source: /target/javawebdemo.war
    destination: /tmp/codedeploy-deployment-staging-area/
  - source: /scripts/configure_http_port.xsl
    destination: /tmp/codedeploy-deployment-staging-area/
hooks:
  ApplicationStop:
    - location: scripts/stop_application
      timeout: 300
  BeforeInstall:
    - location: scripts/install_dependencies
      timeout: 300
  ApplicationStart:
    - location: scripts/write_codedeploy_config.sh
    - location: scripts/start_application
      timeout: 300
  ValidateService:
    - location: scripts/basic_health_check.sh
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2026.png" width="80%" height="80%"/>

git repository에 commit 및 push

AWS CodeBuild → DemoApp 선택 후 빌드 시작

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2027.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2028.png" width="80%" height="80%"/>

### Verify Deployment

AWS CodeDeploy → DemoApp → 배포 그룹 → Development 선택 → 배포생성

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2029.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2030.png" width="80%" height="80%"/>

배포생성

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2031.png" width="80%" height="80%"/>

**InfrastructureStack.DevLocation에서 배포 내용을 확인할 수 있음**

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2032.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2033.png" width="80%" height="80%"/>

## Pipeline

### Create Pipeline

AWS CodePipeline → 파이프라인 생성(DemoApp) → 역할이름 변경 금지

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2034.png" width="80%" height="80%"/>

소스 스테이지

소스 공급자 → AWS CodeCommit선택

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2035.png" width="80%" height="80%"/>

빌드 스테이지 → 공급자: AWS CodeBuild

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2036.png" width="80%" height="80%"/>

배포 스테이지: AWS CodeDeploy

애플리케이션 이름 및 배포그룹 선택(DemoApp, Development) → 파이프라인 생성

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2037.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2038.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2039.png" width="80%" height="80%"/>

### 파이프라인 확인

[A sample web app](http://ec2-18-183-120-10.ap-northeast-1.compute.amazonaws.com/)

Cloud9 DemoApp/src/main/webapp/WEB-INF/pages/index.jsp파일 open

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2040.png" width="80%" height="80%"/>

제목 및 내용 수정

커밋 및 푸시

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2041.png" width="80%" height="80%"/>

에러 테스트

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2042.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2043.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2044.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2045.png" width="80%" height="80%"/>

## 리소스 삭제

### 파이프라인 삭제

AWS CodePipeline → DemoApp → 삭제

CodeDeploy → 애플리케이션 → DemoApp 삭제

CodeBuild → DemoApp 삭제

CodeCommit → DemoApp 레포지토리 삭제

### CDK Destroy

CDK 앱에의해 관리되는 infra 삭제

cd ~/environment/InfrastructureApp

```bash
cd ~/environment/InfrastructureApp
cdk destroy
```

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2046.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2047.png" width="80%" height="80%"/>

S3버킷 및 IAM 권한으로 인해 오류발생

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2048.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2049.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2050.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2051.png" width="80%" height="80%"/>

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2052.png" width="80%" height="80%"/>

### Delete Cloud9

AWS Cloud9 → Development 삭제

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2053.png" width="80%" height="80%"/>

### EC2 인스턴스 종료 확인

<img src="https://github.com/Jaemin-kr/DevOps/blob/main/AWS_Workshop/CI:CD_Workshop/images/Untitled%2054.png" width="80%" height="80%"/>
