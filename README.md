# Docker 개발 워크스테이션 실습

## 미션 목표

터미널에서 파일과 폴더, 권한을 다루고 Docker 이미지와 컨테이너를 실행했다. Dockerfile로 NGINX 웹 서버 이미지를 만들고, 포트 연결, 바인드 마운트, 볼륨의 데이터 보존을 확인했다.

## 실행 환경

- OS: Windows Pro, 빌드 26200
- 터미널: PowerShell, Ubuntu Bash (WSL 2)
- Docker Desktop: 4.97.0
- Docker Engine: 29.9.2
- Git: 2.55.0.windows.5

## 수행 체크리스트

- [x] 터미널 위치·목록·파일·폴더 조작
- [x] 파일과 폴더 권한 변경
- [x] Docker 버전과 엔진 확인
- [x] `hello-world` 실행
- [x] Ubuntu 컨테이너에서 명령 실행
- [x] Dockerfile로 NGINX 커스텀 이미지 빌드
- [x] 포트 매핑으로 웹 페이지 접속
- [x] 바인드 마운트로 파일 변경 반영
- [x] Docker 볼륨의 데이터 보존 확인
- [x] Git 사용자 정보와 기본 브랜치 확인
- [x] GitHub 저장소 생성 및 업로드
- [x] VS Code와 GitHub 연동 완료

## Docker 기본 확인

PowerShell에서 실행했다.

```powershell
docker version
docker run hello-world
docker images
docker ps
docker ps -a
docker logs ubuntu-practice
docker stats --no-stream ubuntu-practice

hello-world 출력에서 Hello from Docker!를 확인했다. Ubuntu 컨테이너에서는 ls와 echo를 실행했다. docker exec로 연 Bash에서 exit한 뒤에도 컨테이너가 계속 실행되는 것을 확인했다.

## 웹 서버 이미지와 포트 연결

site/index.html을 NGINX 웹 서버가 제공하도록 Dockerfile을 작성했다.

FROM nginx:alpine
COPY site/ /usr/share/nginx/html/

빌드와 실행 명령:

docker build -t mission-web:1.0 .
docker run -d --name mission-web-container -p 8080:80 mission-web:1.0

브라우저에서 http://localhost:8080으로 접속해 페이지를 확인했다. 호스트의 8080 포트 요청이 컨테이너의 NGINX 80 포트로 전달된다.

Docker 정보 확인

웹 페이지와 포트 접속

## 바인드 마운트

호스트의 site 폴더를 컨테이너의 NGINX 웹 폴더에 연결했다. 호스트 경로는 내 PC의 프로젝트 위치에 맞게 바꿔야 한다.

docker run -d --name mission-web-bind -p 8081:80 --mount "type=bind,source=C:\Users\bshin\mission-web\site,target=/usr/share/nginx/html,readonly" nginx:alpine

호스트의 site/index.html 내용을 Bind mount works!로 바꾼 뒤 http://localhost:8081을 새로고침했다. 이미지를 다시 빌드하지 않아도 변경된 페이지가 표시됐다.

## Docker 볼륨 데이터 보존

볼륨을 만들고 컨테이너에 연결했다.

docker volume create mission-data
docker run -d --name volume-test1 -v mission-data:/data ubuntu sleep infinity
docker exec volume-test1 sh -c "echo saved-in-volume > /data/hello.txt"
docker exec volume-test1 cat /data/hello.txt

첫 컨테이너를 삭제한 뒤 같은 볼륨을 새 컨테이너에 연결했다.

docker rm -f volume-test1
docker volume ls
docker run -d --name volume-test2 -v mission-data:/data ubuntu sleep infinity
docker exec volume-test2 cat /data/hello.txt

새 컨테이너에서 saved-in-volume이 출력됐다. 컨테이너를 지워도 mission-data 볼륨의 파일은 남아 있음을 확인했다.

## Git 설정

git --version
git config --global --get user.name
git config --global --get user.email
git config --global init.defaultBranch
git init -b main
git status

이름과 이메일이 설정돼 있었고, 기본 브랜치는 main이었다. 로컬 저장소를 만들고 GitHub 저장소에 업로드했다.

## 트러블슈팅

### WSL에 Ubuntu 배포판이 표시되지 않음

- 문제: wsl --list --verbose에서 설치된 배포판이 없다고 표시됐다.
- 원인: WSL 및 Virtual Machine Platform 기능을 켠 뒤 재시작이 필요했다.
- 확인: 재시작 후 Ubuntu 초기 설정이 진행되고 WSL 2로 실행되는 것을 확인했다.
- 해결: 관리자 PowerShell에서 wsl --install -d Ubuntu를 실행하고 재시작했다.

### Ubuntu 터미널에서 Docker 명령을 찾지 못함

- 문제: Ubuntu에서 docker 명령을 찾을 수 없다는 안내가 나왔다.
- 원인: Docker Desktop의 WSL Integration 설정이 연결되지 않았다.
- 확인: PowerShell에서는 docker version과 docker run hello-world가 성공했지만 Ubuntu에서는 실패했다.
- 대안: Docker 명령은 PowerShell에서 실행하고, Linux 명령 실습은 Ubuntu Bash에서 진행했다. WSL Integration 설정은 추가 확인이 필요하다.

## GitHub 제출

- GitHub 저장소: 완료 (`https://github.com/tikita12/E1-1`, `main` 브랜치 업로드 확인)
- VS Code와 GitHub 연동: 완료 (저장소 `origin`이 GitHub 저장소를 가리키는 것 확인)
- VS Code와 GitHub 연동 화면:

  ![VS Code와 GitHub 연동 화면](docs/screenshots/vscode-github-integration.png)
