<p align="center">
  <a href="https://www.jenkins.io/doc/book/installing/docker/" target="blank">
    <img src="https://ddkits.com/sites/files/jenkins.png" width="120" alt="Jenkins Logo" />
  </a>
</p>

<p align="center">
  <a href="https://www.jenkins.io" target="_blank">Jenkins</a> là công cụ mã nguồn mở hỗ trợ triển khai quy trình CI/CD trong phát triển phần mềm.
</p>

---

## 🚀 Mục Tiêu

Thiết lập một môi trường Jenkins sử dụng Docker (Docker-in-Docker) trên EC2 hoặc VPS.

---

## ⚙️ Yêu Cầu

* Máy chủ: EC2 `t3.medium` hoặc VPS có cài sẵn Docker
* Terminal: Nên dùng **Git Bash**
* Docker đã cài đặt

---

## 🧱 Các Bước Cài Đặt

### 1. Tạo Docker network

```bash
docker network create jenkins
```

---

### 2. Chạy Docker-in-Docker (DIND)

> ❗️ Không dùng Git Bash để chạy lệnh này (do vấn đề mount volume `/certs`)

```bash
docker run --name jenkins-docker --rm --detach --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind
```

---

### 3. Tùy chỉnh Jenkins Image

#### a. Tạo Dockerfile

```Dockerfile
FROM jenkins/jenkins:2.504.3-jdk21

USER root

RUN apt-get update && apt-get install -y lsb-release \
  && curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc https://download.docker.com/linux/debian/gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.asc] https://download.docker.com/linux/debian $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list \
  && apt-get update && apt-get install -y docker-ce-cli

USER jenkins

RUN jenkins-plugin-cli --plugins "docker-workflow"
```

#### b. Build image Jenkins mới

```bash
docker build -t jenkins-operator:2.504.3-1 .
```

---

### 4. Chạy Jenkins container

```bash
docker run --name jenkins-operator --restart=on-failure --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client \
  --env DOCKER_TLS_VERIFY=1 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  --publish 8080:8080 \
  --publish 50000:50000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins-operator:2.504.3-1
```

---

### 5. Lấy Jenkins Admin Password

```bash
docker logs jenkins-operator
```

---

### 6. Cấu Hình Jenkins

* Truy cập `http://<your-server-ip>:8080`
* Nhập **Initial Admin Password**
* Cài đặt các plugin **được đề xuất (Recommended plugins)**
* Tạo tài khoản admin (nên đặt username là `root` để dễ phân biệt)

---

## 🔌 Plugin Cần Thiết

| Plugin                                | Mô tả                                                     |
| ------------------------------------- | --------------------------------------------------------- |
| **Amazon ECR**                        | Đăng nhập, đẩy/pull image từ AWS ECR                      |
| **Docker Pipeline**                   | Cung cấp DSL để build/push Docker image trong Jenkinsfile |
| **SSH Agent Plugin**                  | Dùng để `sshagent(credentials: [...])` trong pipeline     |
| **Config File Provider**              | Quản lý và đẩy file cấu hình (.env, config.yaml, ...)     |
| **Strict Crumb Issuer**               | Tránh lỗi 403 khi gọi API Jenkins không có session        |
| **Role-based Authorization Strategy** | Quản lý phân quyền người dùng                             |
| **Stage View**                        | Hiển thị chi tiết các stage trong pipeline UI             |

---

## 🧪 Ví dụ dùng Docker plugin trong Jenkinsfile

```groovy
docker.build("${ecrRegistryPri}:${env.IMAGE_TAG}", "--target ${env.ENV_CODE} --label stage=build .")

docker.withRegistry("https://${ecrRegistryPri}", ecrCredential) {
  dockerImagePrivate.push()
}
```

