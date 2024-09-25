<p align="center">
  <a href="https://www.jenkins.io/doc/book/installing/docker/" target="blank"><img src="../resources/logo.svg" width="120" alt="Jenkins Logo" /></a>
</p>


<p align="center"> <a href="https://www.jenkins.io" target="_blank">Jenkins </a> là một công cụ tự động hóa mã nguồn mở, chủ yếu được sử dụng để triển khai Continuous Integration (CI) và Continuous Delivery (CD) trong quy trình phát triển phần mềm.</p>


## STEP
1. Mở terminal (recommended git bash)
2. Tạo network cho docker

```bash
$ docker network create jenkins
```
3. Chạy docker:bind -> dùng nó vì nó sẽ dùng chính docker engine cho jenkins khi build 
```bash
$ docker run --name jenkins-docker --rm --detach ^
  --privileged --network jenkins --network-alias docker ^
  --env DOCKER_TLS_CERTDIR=/certs ^
  --volume jenkins-docker-certs:/certs/client ^
  --volume jenkins-data:/var/jenkins_home ^
  --publish 2376:2376 ^
  docker:dind
```
4. Tùy chỉnh hình ảnh Docker Jenkins chính thức bằng cách thực hiện hai bước sau:
    a. Tạo 1 Dockerfile với nội dung:   
```bash
    FROM jenkins/jenkins:2.462.2-jdk17
    USER root
    RUN apt-get update && apt-get install -y lsb-release
    RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg
    RUN echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
    RUN apt-get update && apt-get install -y docker-ce-cli
    USER jenkins
    RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"
```


