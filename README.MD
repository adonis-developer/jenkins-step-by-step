<p align="center">
  <a href="https://www.jenkins.io/doc/book/installing/docker/" target="blank"><img src="resources/logo.svg" width="120" alt="Jenkins Logo" /></a>
</p>


<p align="center"> <a href="https://www.jenkins.io" target="_blank">Jenkins </a> là một công cụ tự động hóa mã nguồn mở, chủ yếu được sử dụng để triển khai Continuous Integration (CI) và Continuous Delivery (CD) trong quy trình phát triển phần mềm.</p>


## STEP
  Yêu cầu: có 1 instance EC2 t3.medium hoặc hơn hoặc 1 vps... có cài sẵn docker -> run jenkins bằng docker (docker in docker)
1. Mở terminal (recommended git bash)
2. Tạo network cho docker

```bash
$ docker network create jenkins
```
3. Chạy docker:bind -> dùng nó vì nó sẽ dùng chính docker engine cho jenkins khi build 

```bash
$ docker run --name jenkins-docker --rm --detach --privileged --network jenkins --network-alias docker --env DOCKER_TLS_CERTDIR=/certs --volume jenkins-docker-certs:/certs/client --volume jenkins-data:/var/jenkins_home --publish 2376:2376 docker:dind
```
4. Tùy chỉnh hình ảnh Docker Jenkins chính thức bằng cách thực hiện hai bước sau:
    
  a. Tạo 1 Dockerfile với nội dung:   
```bash
    FROM jenkins/jenkins:2.462.2-jdk17
    RUN apt-get update && apt-get install -y lsb-release
    RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg
    RUN echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
    RUN apt-get update && apt-get install -y docker-ce-cli
    RUN jenkins-plugin-cli --plugins "docker-workflow"
```
    Dockerfile này sẽ tạo ra 1 image jenkins đã cài sẵn các plugin docker
     (plugin để dùng các lệnh pipeline trong Jenkinsfile)
    vd: 
      docker.build("${ecrRegistryPri}:${env.IMAGE_TAG}", "--target ${env.ENV_CODE} --label stage=build .")
      docker.withRegistry("https://" + ecrRegistryPri, ecrCredential) {  
       dockerImagePrivate.push()
      }
    
  b. Xây dựng một hình ảnh docker mới từ Dockerfile này và gán cho hình ảnh một tên có ý nghĩa.
    ví dụ: "jenkins-operator:2.462"
```bash
$  docker build -t jenkins-operator:2.462 .
```
5. Chạy image đã build ở trên với lệnh: 

```bash

```bash
$  docker run --name jenkins-operator --restart=on-failure --detach --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1  --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro  --publish 8080:8080 --publish 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock jenkins-operator:2.462
```

6. Kiểm tra logs của container mới đc chạy để lấy init password
```bash
$ docker logs jenkins-operator
```

7. Hãy chọn plugin được recommended để jenkins tải các plugin này cho bạn.

8. Sau khi jenkins hoàn thành tải các plugin cho bạn nó sẽ bắt bạn tạo 1 root account, nên đặt username là root để dễ phân biệt với các account khác sau này.

