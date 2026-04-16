# 개발 워크스테이션 구축 미션

## 1. 프로젝트 개요
터미널, Docker, Git을 활용하여 어디서나 재현 가능한 개발 환경을 구축하고, 컨테이너 기반의 웹 서버 운영 및 데이터 영속성을 검증하는 프로젝트입니다.

---

## 2. 실행 환경
* **OS:** Windows 10/11 (Git Bash / MINGW64)
* **Docker:** OrbStack (또는 Docker Desktop)
* **Git 버전:** git version 2.47.1.windows.2
* **터미널:** Git Bash (MINGW64)

---

## 3. 수행 항목 체크리스트
- [x] 터미널 기본 조작 및 파일 관리
- [x] 파일 및 디렉토리 권한 설정 (chmod)
- [x] Docker 설치 및 기본 환경 점검
- [x] 커스텀 Dockerfile 기반 이미지 빌드
- [x] 포트 매핑을 통한 웹 서버 접속
- [x] Docker 볼륨을 이용한 데이터 영속성 검증
- [x] Git 사용자 설정 및 GitHub 저장소 연동

---

## 4. 수행 기록 및 검증

### 4.1 Git 설정 확인
사용자 정보 및 기본 브랜치 설정을 완료하였습니다.

```bash
$ git config --list
user.name=heeyoung35
user.email=kheeyoung35@gmail.com
core.autocrlf=true
init.defaultbranch=master
safe.directory=D:/codyssey/codysseyworkstation-mission

remote.origin.url=https://github.com/heeyoung35/codysseyworkstation-mission.git
branch.main.remote=origin
branch.main.merge=refs/heads/main
user.name=heeyoung35
user.email=kheeyoung35@gmail.com
```

**GitHub 원격 저장소 연동 및 최신 커밋 확인:**
![GitHub 연동 확인](images/17_git_github_sync_check.png)

---

### 4.2 터미널 조작 및 권한 실습 (로컬 환경)
터미널을 이용한 디렉토리 제어 및 파일 생성 로그입니다. 윈도우 환경의 특성상 `chmod` 명령어가 기호로 출력되지 않는 현상을 확인하였습니다.

```bash
$ pwd
/d/codyssey/codysseyworkstation-mission

$ mkdir mission_logs
$ cd mission_logs
$ touch test.txt

$ ls -l test.txt
-rw-r--r-- 1 gram 197121 0 Mar 31 17:28 test.txt

$ chmod 755 test.txt
$ ls -l test.txt
-rw-r--r-- 1 gram 197121 0 Mar 31 17:28 test.txt  # 윈도우 환경상 변화 없음
```

**파일(test.txt) 권한 변경 전/후 확인:**
![파일 chmod 실습](images/05_windows_file_chmod_test.png)

**디렉토리(mission_logs) 권한 변경 전/후 확인:**
![디렉토리 chmod 실습](images/04_windows_chmod_limit.png)

---

### 4.3 Docker 설치 및 기본 환경 점검
Docker 엔진의 정상 작동 여부를 `docker info`와 `hello-world` 실행으로 검증하였습니다.

**Docker 주요 정보:**
* **Server Version:** 28.1.1
* **Operating System:** Docker Desktop
* **Kernel Version:** 6.6.87.1-microsoft-standard-WSL2

**docker --version 확인:**
![docker version 확인](images/01_docker_version_check.png)

**docker info 상세 출력:**
![docker info 출력](images/02_docker_info_detail.png)

```bash
$ docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

**hello-world 실행 전체 로그:**
![hello-world 실행 성공](images/03_docker_hello_world.png)

---

### 4.3-1 Docker 운영 명령 실행 기록
Docker 엔진 상태를 점검하는 주요 운영 명령어를 실행하고 결과를 기록하였습니다.

**docker images (로컬 이미지 목록):**
![docker images 출력](images/07_docker_images.png)

**docker ps -a (전체 컨테이너 목록):**
![docker ps -a 출력](images/08_docker_ps_a.png)

**docker logs (컨테이너 실행 로그):**
![docker logs 출력](images/09_docker_logs.png)

**docker stats --no-stream (리소스 사용량):**
![docker stats 출력](images/10_docker_stats.png)

---

### 4.4 Docker 컨테이너 내 권한 실습
로컬(Windows) 환경에서 확인이 불가능했던 `chmod` 동작을 Ubuntu 컨테이너 내부(Linux 환경)에서 성공적으로 재검증하였습니다.

```bash
# Ubuntu 컨테이너 실행 및 권한 변경 테스트
$ docker run -it ubuntu
root@2f9e1e7f2886:/# touch docker_test.txt
root@2f9e1e7f2886:/# chmod 755 docker_test.txt
root@2f9e1e7f2886:/# ls -l docker_test.txt
-rwxr-xr-x 1 root root 0 Mar 31 09:43 docker_test.txt  # 실행 권한(x) 확인
```

**Ubuntu 컨테이너 내부 chmod 성공 확인:**
![ubuntu chmod 성공](images/06_ubuntu_chmod_success.png)

---

### 4.5 커스텀 Dockerfile 빌드 및 실행
사용자 정의 HTML 파일을 포함하는 커스텀 이미지를 빌드하고, 포트 매핑(8080:80)을 통해 호스트 브라우저에서 접속을 확인하였습니다.

#### Dockerfile
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### 빌드 및 실행 명령어
```bash
# 1. 빌드 명령어
$ docker build -t my-nginx:1.0 .

# 2. 실행 명령어
$ docker run -d -p 8080:80 my-nginx:1.0

# 3. 접속 확인
$ curl http://localhost:8080
```

#### 접속 증명
![웹 서버 성공 화면](images/web-server-success.png)

---

### 4.5-1 바인드 마운트(Bind Mount) 실습
호스트 디렉토리를 컨테이너에 직접 연결하여, **컨테이너를 재빌드하지 않고도 호스트 파일 변경이 즉시 반영**되는 것을 검증하였습니다.

```bash
# 바인드 마운트로 nginx 컨테이너 실행
# (MSYS_NO_PATHCONV=1: Git Bash 경로 자동변환 방지)
MSYS_NO_PATHCONV=1 docker run -d -p 8081:80 --name bind-test \
  -v /c/Users/gram/codyssey/bind-test:/usr/share/nginx/html \
  nginx:alpine

# 변경 전 파일 설정
echo '<h1>Before Change</h1>' > /c/Users/gram/codyssey/bind-test/index.html
# → http://localhost:8081 접속 확인

# 호스트 파일만 수정 (컨테이너 재시작 없음)
echo '<h1>After Change!</h1>' > /c/Users/gram/codyssey/bind-test/index.html
# → 브라우저 새로고침만으로 즉시 반영 확인
```

**변경 전 (Before Change) — localhost:8081 접속:**
![바인드 마운트 변경 전](images/07_bind_mount_before.png)

**변경 후 (After Change!) — 컨테이너 재시작 없이 즉시 반영:**
![바인드 마운트 변경 후](images/08_bind_mount_after.png)

---

### 4.6 Docker 볼륨 영속성 검증
Docker 볼륨을 생성하여 컨테이너가 삭제된 이후에도 데이터가 보존되는 것을 검증하였습니다.

```bash
# 1. 볼륨 생성
docker volume create mydata

# 2. 첫 번째 컨테이너 실행 및 데이터 쓰기
docker run -d --name vol-test1 -v mydata:/data ubuntu sleep infinity
docker exec vol-test1 bash -c "echo 'hello volume' > /data/hello.txt && cat /data/hello.txt"

# 3. 첫 번째 컨테이너 삭제
docker rm -f vol-test1

# 4. 두 번째 컨테이너로 동일 볼륨 재연결 → 데이터 유지 확인
docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity
docker exec vol-test2 bash -c "cat /data/hello.txt"
# → "hello volume" 출력 = 볼륨 영속성 증명 완료
```

**① 볼륨 생성 (`docker volume create mydata`):**
![볼륨 생성](images/11_volume_create.png)

**② 첫 번째 컨테이너(vol-test1) 실행:**
![vol-test1 실행](images/12_container1_write_data.png)

**③ 볼륨에 데이터 쓰기 및 확인 (`hello volume` 출력):**
![데이터 쓰기 확인](images/13_container1_output_check.png)

**④ vol-test1 컨테이너 삭제:**
![컨테이너 삭제](images/14_container1_remove.png)

**⑤ 두 번째 컨테이너(vol-test2) 동일 볼륨으로 재연결:**
![vol-test2 재연결](images/15_container2_reconnect.png)

**⑥ 컨테이너 삭제 후에도 데이터 유지 확인 (영속성 증명):**
![볼륨 영속성 성공](images/16_volume_persistence_success.png)

---

## 5. 핵심 개념 설명 및 설계 기준

### 5.1 프로젝트 디렉토리 구조 설계

```
codysseyworkstation-mission/
├── Dockerfile                 # 커스텀 이미지 빌드 정의
├── docker-compose.yml         # 전체 컨테이너 환경 설정 (재현 가능)
├── index.html                 # 웹 서버 콘텐츠
├── README.md                  # 프로젝트 전체 설명서
├── mission_logs/              # 실습 과정 스크린샷 및 로그
│   ├── 01_docker_version_check.png
│   ├── 02_docker_info_detail.png
│   └── ... (실습 기록)
├── html/                      # 바인드 마운트용 호스트 디렉토리
│   └── index.html
└── config/                    # 설정 파일 (권한 설정 예시)
    └── nginx.conf
```

구성 기준:

| 디렉토리/파일 | 목적 | 선택 이유 |
|---|---|---|
| Dockerfile | 이미지 빌드 방식 명확화 | 재현 가능성 + 버전 관리 |
| docker-compose.yml | 포트/볼륨 설정 자동화 | 한 줄 명령어로 전체 환경 구성 |
| mission_logs/ | 실습 과정 기록 | 학습 경로 추적 + 검증 증거 |
| html/ | 바인드 마운트 소스 | 개발 중 즉시 반영 (Hot Reload) |
| config/ | 설정 파일 관리 | 권한 설정 및 환경 설정 중앙화 |

---

### 5.2 docker-compose.yml을 통한 재현 가능한 설정

```yaml
version: '3.8'

services:
  web:
    image: my-nginx:1.0
    container_name: nginx-server
    ports:
      - "8080:80"              # 호스트 포트 8080 → 컨테이너 포트 80
    volumes:
      - ./html:/usr/share/nginx/html    # 바인드 마운트 (개발용)
      - web-logs:/var/log/nginx         # 명명된 볼륨 (로그 영속성)
    environment:
      - NGINX_HOST=localhost
    networks:
      - app-network
    restart: unless-stopped

volumes:
  web-logs:                    # Docker가 관리하는 명명된 볼륨

networks:
  app-network:
    driver: bridge
```

재현 방법:

```bash
# 1. 프로젝트 디렉토리로 이동
cd codysseyworkstation-mission

# 2. 한 줄 명령어로 전체 환경 구성
docker-compose up -d

# 3. 서비스 상태 확인
docker-compose ps

# 4. 로그 확인
docker-compose logs -f web

# 5. 환경 종료
docker-compose down
```

재현 가능성의 이점:
- ✅ 다른 개발자도 동일한 환경 구성 가능
- ✅ CI/CD 파이프라인에 통합 용이
- ✅ 포트/볼륨 설정이 코드로 버전 관리됨
- ✅ 환경 변수 변경 시 Dockerfile 재빌드 불필요

---

### 5.3 이미지(Image) vs 컨테이너(Container) 개념 비교

| 관점 | 이미지(Image) | 컨테이너(Container) |
|---|---|---|
| 정의 | 애플리케이션 실행에 필요한 모든 것을 담은 템플릿 | 이미지를 기반으로 실행되는 격리된 프로세스 |
| 빌드 | Dockerfile → docker build 명령어로 생성 | 이미지를 docker run으로 실행하여 생성 |
| 실행 | 실행 불가능 (설계도) | 실행 가능한 프로세스 |
| 변경 | 변경 후 docker build 재실행 필요 | 실행 중 파일 변경 가능 (휘발성) |
| 저장 | 로컬 또는 Docker Hub/레지스트리에 저장 | 컨테이너 삭제 시 데이터 소실 (볼륨 미사용 시) |
| 크기 | 수 MB ~ 수 GB (압축된 형태) | 실행 중인 프로세스 (메모리 사용) |

실제 비유:

```
이미지    = 건축 설계도
컨테이너  = 설계도로 지은 실제 건물

같은 설계도(이미지)로 여러 건물(컨테이너)을 지을 수 있습니다.
```

---

## 6. 포트 매핑과 네트워크 격리

### 6.1 컨테이너 내부 포트로 직접 접속할 수 없는 이유

문제 상황:
```bash
# ❌ 실패: 호스트에서 컨테이너 포트로 직접 접속 불가
$ curl http://localhost:80
curl: (7) Failed to connect to localhost port 80: Connection refused

# ✅ 성공: 포트 매핑을 통한 접속
$ curl http://localhost:8080
<html>...</html>
```

원인 분석:

| 원인 | 설명 |
|---|---|
| 네트워크 격리 | 컨테이너는 독립된 네트워크 네임스페이스를 가짐 |
| 호스트와 분리 | 호스트의 localhost와 컨테이너의 localhost는 다른 네트워크 |
| 포트 매핑 필요 | 호스트 OS가 중개자 역할을 해야 함 |

포트 매핑의 동작 원리:
```bash
# 포트 매핑 설정
docker run -d -p 8080:80 my-nginx:1.0
#              ↑    ↑
#           호스트  컨테이너
#            포트    포트

# 동작 원리:
# 호스트:8080 → (Docker 중개) → 컨테이너:80
```

검증:
```bash
# 컨테이너 내부에서는 접속 가능
$ docker exec <container-id> curl http://localhost:80
# ✅ 성공

# 호스트에서 직접 접속은 불가능
$ curl http://localhost:80
# ❌ 실패

# 포트 매핑을 통해서만 접속 가능
$ curl http://localhost:8080
# ✅ 성공
```

---

## 7. 절대 경로 vs 상대 경로 선택 기준

### 7.1 경로 선택 기준표

| 상황 | 절대 경로 | 상대 경로 | 선택 이유 |
|---|---|---|---|
| docker run 명령어 | ✅ 권장 | ❌ 비권장 | 어디서 실행해도 동일한 위치 지정 |
| docker-compose.yml | ❌ 비권장 | ✅ 권장 | 파일 위치 기준으로 자동 조정 |
| Dockerfile | ❌ 비권장 | ✅ 권장 | 이미지 빌드 컨텍스트 기준 |
| 자동화 스크립트 | ✅ 권장 | ❌ 비권장 | 스크립트 위치와 무관하게 동작 |
| 프로젝트 이동 | ❌ 경로 변경 필요 | ✅ 자동 조정 | 프로젝트 폴더 이동 시 유리 |

### 7.2 실제 예시

❌ 절대 경로 사용 (재현 어려움):
```bash
# Windows 절대 경로
docker run -v /c/Users/gram/codyssey/bind-test:/usr/share/nginx/html nginx

# 문제점:
# - 다른 사용자는 경로 변경 필요
# - 다른 PC에서는 경로가 다름
# - 재현 불가능
```

✅ 상대 경로 사용 (docker-compose.yml에서):
```yaml
version: '3.8'
services:
  web:
    image: nginx:alpine
    volumes:
      - ./html:/usr/share/nginx/html  # 프로젝트 폴더 기준
      - ./config:/etc/nginx/conf.d    # 자동으로 조정됨
```

실행 결과:
```bash
# 프로젝트 폴더 구조
codysseyworkstation-mission/
├── docker-compose.yml
├── html/
│   └── index.html
└── config/
    └── nginx.conf

# 어디서 실행하든 동일하게 작동
$ cd /d/codyssey/codysseyworkstation-mission
$ docker-compose up -d
# ✅ ./html → /d/codyssey/codysseyworkstation-mission/html 로 자동 해석
```

---

## 8. 파일 권한 숫자 표기 규칙

### 8.1 권한 숫자 규칙 (3자리 = 소유자/그룹/기타)

```
chmod 755 = rwxr-xr-x
       ↓
    7  5  5
    ↓  ↓  ↓
  소유자 그룹 기타
```

권한 값 계산:

| 기호 | 값 | 의미 |
|---|---|---|
| r (read) | 4 | 읽기 권한 |
| w (write) | 2 | 쓰기 권한 |
| x (execute) | 1 | 실행 권한 |

조합 예시:

| 숫자 | 계산 | 권한 | 의미 |
|---|---|---|---|
| 7 | 4+2+1 | rwx | 읽기 + 쓰기 + 실행 |
| 6 | 4+2 | rw- | 읽기 + 쓰기 |
| 5 | 4+1 | r-x | 읽기 + 실행 |
| 4 | 4 | r-- | 읽기만 |
| 0 | 0 | --- | 권한 없음 |

### 8.2 실제 적용 사례

```bash
# 755 = rwxr-xr-x (실행 가능한 스크립트)
chmod 755 script.sh
ls -l script.sh
# -rwxr-xr-x  script.sh
# 소유자: 읽기+쓰기+실행 / 그룹: 읽기+실행 / 기타: 읽기+실행

# 644 = rw-r--r-- (일반 설정 파일)
chmod 644 config.txt
ls -l config.txt
# -rw-r--r--  config.txt
# 소유자: 읽기+쓰기 / 그룹: 읽기만 / 기타: 읽기만

# 700 = rwx------ (소유자만 접근 가능)
chmod 700 private/
ls -ld private/
# drwx------  private/
# 소유자: 읽기+쓰기+실행 / 그룹: 권한없음 / 기타: 권한없음
```

### 8.3 권한 설정 가이드

| 파일 유형 | 권한 | 설명 |
|---|---|---|
| 실행 스크립트 | 755 | 소유자는 모든 권한, 타인은 읽기+실행만 |
| 일반 파일 | 644 | 소유자는 읽기+쓰기, 타인은 읽기만 |
| 설정 파일 | 600 | 소유자만 읽기+쓰기 (보안) |
| 디렉토리 | 755 | 소유자는 모든 권한, 타인은 읽기+실행만 |
| 개인 디렉토리 | 700 | 소유자만 접근 가능 |

---

## 9. 포트 충돌 진단 및 해결

### 9.1 "호스트 포트가 이미 사용 중" 진단 순서

**1단계: 포트 사용 상태 확인**
```bash
# Windows 환경
netstat -ano | findstr :8080
# TCP    0.0.0.0:8080    0.0.0.0:0    LISTENING    12345

# Mac/Linux 환경
lsof -i :8080
# COMMAND   PID USER   FD  TYPE DEVICE SIZE/OFF NODE NAME
# nginx   12345 root    6u  IPv4  0x123      0t0  TCP *:8080 (LISTEN)
```

**2단계: Docker 컨테이너 확인**
```bash
# 실행 중인 모든 컨테이너 확인
docker ps -a

# 특정 포트를 사용하는 컨테이너 찾기
docker ps --format "table {{.Names}}\t{{.Ports}}"
# NAMES           PORTS
# nginx-server    0.0.0.0:8080->80/tcp
```

**3단계: 포트 변경 또는 기존 컨테이너 종료**
```bash
# 방법 A: 충돌하는 컨테이너 중지 후 재시작
docker stop nginx-server
docker run -d -p 8080:80 my-nginx:1.0

# 방법 B: 다른 포트로 변경하여 실행
docker run -d -p 8082:80 my-nginx:1.0
# → http://localhost:8082 로 접속

# 방법 C: 프로세스 직접 종료 (Windows)
taskkill /PID 12345 /F
```

---

## 10. 트러블슈팅

### Case 1: Git 설정 시 'safe.directory' 에러

**문제:** 저장소 경로를 Git이 신뢰하지 않아 명령어가 거부됨.

**원인:** Git 보안 정책 강화로 인한 미등록 경로 차단.

**해결:** `git config --global --add safe.directory` 명령으로 경로 등록.

---

### Case 2: 로컬 환경(Windows)에서의 chmod 미적용

**문제:** chmod 실행 후에도 `ls -l` 기호가 변하지 않음.

**원인:** Windows NTFS 파일 시스템과 POSIX 권한 체계의 불일치.

**해결:** Docker 리눅스 컨테이너 환경을 활용하여 명령의 정상 동작을 교차 검증함.

---

### Case 3: Git Pull 시 머지 충돌(Merge Conflict)

**문제:** 원격과 로컬의 파일 내용이 달라 `Automatic merge failed` 발생.

**원인:** GitHub 초기 생성 파일과 로컬 신규 파일 간의 충돌.

**해결:** `git merge --abort` 후 수동 병합(Conflict 해결) 수행.

---

### Case 4: Git Bash MSYS 경로 자동변환으로 인한 바인드 마운트 실패

**문제:** Git Bash에서 `-v` 옵션으로 바인드 마운트를 설정했으나, 브라우저에서 nginx 기본 페이지(Welcome to nginx!)가 그대로 출력됨.

**원인:** Git Bash(MSYS2 환경)는 `/usr/share/nginx/html` 같은 경로를 자동으로 Windows 경로로 변환함.
`/usr` → `C:\Program Files\Git\usr` 로 변환되어 Docker에 엉뚱한 경로가 전달됨.

`docker inspect`로 확인한 실제 마운트 결과:
```bash
$ docker inspect bind-test --format='{{json .Mounts}}'
[{
  "Source": "C:\\Users\\gram\\codyssey\\bind-test;C",
  "Destination": "\\Program Files\\Git\\usr\\share\\nginx\\html"
}]
# → Destination이 컨테이너 내부 경로가 아닌 Git 설치 경로로 잘못 변환됨
```

**해결:** `MSYS_NO_PATHCONV=1` 환경변수를 앞에 붙여 경로 자동변환을 비활성화한 후 실행.

```bash
docker rm -f bind-test

MSYS_NO_PATHCONV=1 docker run -d -p 8081:80 --name bind-test \
  -v /c/Users/gram/codyssey/bind-test:/usr/share/nginx/html \
  nginx:alpine

# 내부 파일 확인으로 마운트 성공 검증
MSYS_NO_PATHCONV=1 docker exec -it bind-test ls /usr/share/nginx/html
# → index.html 출력 확인 (바인드 마운트 정상 동작)
```
