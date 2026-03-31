# 개발 워크스테이션 구축 미션

## 1. 프로젝트 개요
터미널, Docker, Git을 활용하여 어디서나 재현 가능한 개발 환경을 구축하고, 컨테이너 기반의 웹 서버 운영 및 데이터 영속성을 검증하는 프로젝트입니다.

---

## 2. 실행 환경
* **OS:** Windows 10/11 (Git Bash / MINGW64)
* **Docker:** OrbStack (또는 Docker Desktop)
* **Git 버전:** [git version 2.47.1.windows.2]
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