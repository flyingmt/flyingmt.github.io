### Kubernetes

설치하기

- minikube를 이용해 로컬에서 사용하기

  - https://github.com/kubernetes/minikube
  - 설치
    - 참조 : https://minikube.sigs.k8s.io/docs/start/
    - Choco를 사용하여 설치하기
    ```
    > choco install minikube
    ```
    - Virtualbox를 기본으로 사용하게 하기
    ```
    > minikube config set vm-driver virtualbox
    ```
  - 가상화 소프트웨어를 미리 설치해야 함
    - 예) Virtual Box
  - 명령들
    - 클러스터 시작
    ```
    > minikube start
    ```
    - 응용프로그램 배포
    ```
    > kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
    > kubectl expose deployment hello-minikube --type=NodePort --port=8080
    ```
    - 응용프로그램 정보 보기
    ```
    > kubectl get services hello-minikube
    ```
    - 응용프로그램 접근하기
    ```
    > minikube service hello-minikube
    ```
    - 클러스터 종료
    ```
    > minikube stop
    ```
    - 클러스터 VM 삭제
    ```
    > minikube delete
    ```

- Docker Client을 이용해서 사용하기

  - Docker (Edge 버전) 설치
  - 환경 설정에서 Kubernetes 메뉴에서 설정

- kops를 이용해 AWS에서 사용하기 (Kubeadm도 있음)

  - AWS는 t2.micro를 한달에 총 750시간 무료로 사용할 수 있게 해줌
  - http://aws.amazon.com
  - kops는 맥과 리눅스에서만 사용 가능

    - 윈도우: Virtualbox와 Vagrant 필요 (choco 통해 설치)
    - 가상 머신 만들기
      - 가상 머신을 만들 폴더 생성하여 이동
      - Vagrant을 이용해서 VM 만듬
      ```
      > vagrant init ubuntu/xenial64
      ```
      - Vagrant 실행
      ```
      > vagrant up
      ```
      - Vagrant 로그인 (윈두오는 openSSH 설치 필요)
      ```
      > vagrant ssh-config
      > vagrant ssh
      ```
      - Vagrant 종료
      ```
      > vagrant halt
      ```
    - kops 설치 (vagrant 안에서)
      - http://github.com/kubernetes/kops
        - 여기 Release에서 kops-linux-amd64를 받아서 설정함
        ```
        > wget http://경로/kops-linux-amd64
        > chmod +x kops-linux-amd64
        > sudo mv kops-linux-amd64 /usr/local/bin/
        > sudo mv /usr/local/bin/kops-linux-amd64 /usr/local/bin/kops
        ```
    - AWS 툴 설치 (vagrant 안에서)
      - python-pip 설치
      ```
      > sudo apt update
      > sudo apt-get install python-pip
      ```
      - AWS 툴 설치
      ```
      > sudo pip install awscli
      ```
    - AWS 서비스 가입
      - AWS에 가서 서비스 가입
      - IAM 서비스로 간다
      - 사용자(Users)로 가서 새 사용자 추가한다.
        - Username : kops
        - Programmatic access 선택
        - 권한 설정
          - 기존 정책 직접 연결(Attach existing policies directly)
          - AdministratorAccess 선택
        - 만들고 나면 엑세스키와 비밀 엑세스키를 복사한다.
    - AWS 툴 설정
      - 엑세스키와 비밀 엑세스키 설정
      ```
      > aws configure
      ```
      - 실제로 ~/.aws/ 경로에 설정파일을 저장이 된다.
    - AWS에서 kops-state 저장을 위해 S3 인스턴스를 만든다.
      - Bucket Name : kops-state-b429b77 (뒤에는 랜덤하게)
    - kops를 사용하기 위해서는 AWS에서 DNS 설정
      - 일단 도메인이 있어야 한다.
      - Route 53 에서 도메인 등록 함
      - Route 53에서 DNS 관리로 들어간다.
        - 호스팅 영역 생성(Create Hosted Zone)으로 들어간다
          - Domain Name : kubernetes.내도메인
          - 위 도메인을 NS 타입으로 Route 53을 바라보게 한다.
          - 다음 명령으로 확인
          ```
          > host -t NS kubernetes.내도메인
          ```
    - kops를 이용해서 클러스터 생성하기
      - kubectl 설치
      ```
      > curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
      > chmod +x ./kubectl
      > sudo mv ./kubectl /usr/local/bin/kubectl
      > kubectl version --client
      ```
    - SSH 키 생성하기
      ```
      > ssh-keygen -f .ssh/id_rsa
      > cat .ssh/id_rsa.pub
      ```
    - 생성 인스턴스 설정
      ```
      > kops create cluster --name=kubernetes.내도메인 --state=s3://kops-state-b429b77 --zones=ap-northeast-2a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.내도메인
      ```
    - 인스턴스 생성
      ```
      > kops update cluster kubernetes.내도메인 --yes --state=s3://kops-state-b429b77
      ```
    - 인스턴스 생성 확인
      ```
      > kops validate cluster --state=s3://kops-state-b429b77
      ```
    - 쿠버네티스 인스턴스 확인
      ```
      > kubectl get node
      ```
    - 응용프로그램 배포

      ```
      > kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
      > kubectl expose deployment hello-minikube --type=NodePort --port=8080
      ```

    - 응용프로그램 정보 보기
      ```
      > kubectl get services hello-minikube
      ```
    - 응용프로그램 접근하기

      - AWS엠서 node 인스턴스에 대해서 보안포트를(내 IP에 대해서만 염) 연다 (예 32347)
      - AWS에서 node의 IP 주소를 알아내 브라우저에서 확인한다.

    - 클러스터 삭제
      ```
      > kops delete cluster kubernetes.내도메인 --state=s3://kops-state-b429b77
      > kops delete cluster kubernetes.내도메인 --state=s3://kops-state-b429b77 --yes
      ```

### Containers

- Docker Engine을 이용해야 한다
- Docker 설치 (vagrant 내)
  ```
  > sudo apt-get install docker.io
  > sudo apt-get install git
  ```
- Dockerfile
  ```yaml
  FROM node:4.6
  WORKDIR /app
  ADD ./app
  RUN npm install
  EXPORT 3000
  CMD npm start
  ```
  ```
  > docker build .
  ```
- Docker 이미지 만들어보기
  - 만들 프로그램 다운로드
    ```
    > git clone https://github.com/wardviaene/docker-demo
    > cd docker-demo
    ```
  - 이미지 만들기
    ```
    sudo docker build .
    ```
  - 이미지 실행하기
    ```
    > docker run -p 3000:3000 -lt 생성이미지ID
    ```
