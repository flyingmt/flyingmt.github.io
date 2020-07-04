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
- Dockerfile 내용
  ```yaml
  FROM node:4.6
  WORKDIR /app
  ADD ./app
  RUN npm install
  EXPORT 3000
  CMD npm start
  ```
  Docker 빌드 명령
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
- Docker Registry
  - Docker를 로컬에서만 사용한다면 Docker Registry가 필요 없음
  - 만약 큐버네티스에서 사용하려면 Docker Hub 같은 Docker Registry에 등록해야 한다
  - Docker Hub에 등록하기 위해서는
  ```
  > docker login
  > docker tag imageid your-login/docker-demo
  > docker push your-login/docker-demo
  ```
  - Dockerfile 빌드시 tag을 정할 수 있음
  ```
  > docker build -t your-login/docker-demo .
  ```

- Docker 주의사항
  - 하나의 컨테이너에 하나의 프로세스가 되도록 하자
  - 컨테이너안에 내용은 저장되지 않는다 (volumes를 사용해서 할 수 있음)

- Docker Image를 Kubernetes Cluster에 사용하기
  - 컨테이너 기반 이미지를 실행하기전 pod 정의를 해야 함
    - Pod는 Kubernetes에서 실행하는 응용프로그램을 정의함
    - Pod는 응용프로그램을 만드는 하나이상의 연결된 컨테이너를 가질 수 있음
    - 이들 응용프로그램은 그들의 로컬 포트 번호를 사용하여 통신함
  - Pod 파일인 "pod-helloworld.yml"을 만든다
  ```yaml
  apiVersoin: v1
  kind: Pod
  metadata:
    name: nodehelloworld.example.com
    labels:
      app: helloworld
  spec:
    containers:
    - name: docker-demo
      image: flyingmt/docker-demo
      ports:
      - containerPort: 3000
  ```
  - kubctl 사용하여 Kubernetes Cluster에 pod 생성
  ```
  > kubectl create -f docker-demo/pod-helloworld.yml
  ``` 
  - 유용한 명령어들
    - kubectl get node : 실행되고 있는 node들 가져오기
    - kubectl get service : 실행되고 있는 service들 가져오기
    - kubectl get pod : 실행되고 있는 모든 pod들 가져오기
    - kubectl describe pod podname : podename의 상세 정보 가져오기 
    - kubectl expose pod podename --port-444 --name-frontend : Pod의 포트를 노출 (새로운 서비스 만듬)
    - kubectl port-forward podname 8080:3000 : 노출된 pod의 포트를 여러분의 로컬에 포트 포워드하기
    - kubectl attach podname -i : Pod에 붙기
    - kubectl exec podname -- command : Pod에서 명령 실행 (-c로 컨테이터 지정가능)
    - kubectl label pods podename mylable=awesome : 새로운 레이블을 pod에 붙이기
    - kubectl run -i --tty busybox --image=busybox --restart=Never -- sh : Pod에 셀 실행

  - minikube에서는 다음과 같이 포트를 노출 시킬수 있음
  ```
   > kubectl expose pod nodehelloworld.example.com --type=NodePort --name nodehelloworld-service
   > minikube service nodehelloworld-service --url
  ```
  - AWS에서는 ELB를 사용할 수 있도록 따로 서비스를 만든다
    - pod : helloworld.yml
    ```yaml
    apiVersoin: v1
    kind: Pod
    metadata:
      name: nodehelloworld.example.com
      labels:
        app: helloworld
    spec:
      containers:
      - name: k8s-demo
        image: wardviaene/k8s-demo
        ports:
        - name: nodejs-port
          containerPort: 3000
    ```
    - service : helloworld-service.yml
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name : helloworld-service
    spec:
      ports:
      - port: 80
        targetPort: nodejs-port
        protocal: TCP
      selector:
        app: helloworld
      type: LoadBalancer
    ```
