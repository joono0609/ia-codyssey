# ia-codyssey
Github 와 codyssey 와 연동하기 위해 만들어진 Repository 입니다 

# git 최신버전으로 윈도우 설치
# docker 최시버전으로 윈도우amd 버전 설치
# vscode 는 wsl 에서도 활용할 수 있게 wsl extension 설치 후 확인


#프로젝트 개요(미션 목표 요약)
#실행 환경(OS/쉘/터미널, Docker 버전, Git 버전)
#수행 항목 체크리스트(터미널/권한/Docker/Dockerfile/포트/볼륨/Git/GitHub)
#검증 방법(어떤 명령으로 무엇을 확인했는지) + 결과 위치 링크
#트러블슈팅 2건 이상(문제 → 원인 가설 → 확인 → 해결/대안)7
#기술 문서만 읽어도 전체 수행 내용을 파악할 수 있어야 한다.


#프로젝트 개요

이 프로젝트는 우분를 이용해 파일과 권한을 조작하고 Docker desktop 으로 콘테이너를 실행 및 관리하며 직접 작성한 Dockerfile 로  NGINX 웹서버 이미지를 제작하는 실습이고 포트 매핑으로 웹페이지에 접속하고 바인드 마운트로 원본 파일 변경을 반영하고 Docker 볼륨을 통해 컨테이너 삭제 후 에도 데이터가 유지된다.

#실행 환경(OS/쉘/터미널, Docker 버전, Git 버전)

wsl --install -d Ubuntu-22.04
==> id/passwd codyssey


#수행 항목 체크리스트(터미널/권한/Docker/Dockerfile/포트/볼륨/Git/GitHub)

윈도우에는 chmod 가 없고 별도 있는것으로 알고 있음 -> wsl 이용 우분튜 설치

Docker 설치 및 기본 점검
Docker 버전 확인 결과를 기록한다. (docker --version)
Docker 데몬 동작 여부 확인 결과를 기록한다. (docker info 또는 동등 점검)
Docker 기본 운영 명령 수행
이미지: 다운로드/목록 확인 (예: docker images)
컨테이너: 실행/중지/목록 확인 (예: docker ps, docker ps -a)
운영: 로그 확인 (예: docker logs), 리소스 확인 (예: docker stats)
수행 명령과 출력 결과를 기술 문서에 남긴다.



#검증 방법(어떤 명령으로 무엇을 확인했는지) + 결과 위치 링크

위 내용이 확정되지 않으면 약간 틀림

#트러블슈팅 2건 이상(문제 → 원인 가설 → 확인 → 해결/대안)


#기술 문서만 읽어도 전체 수행 내용을 파악할 수 있어야 한다.


## 1) 실행 환경
- OS: Ubuntu 22.04     
- Shell: bash
- Docker: 29.9.2
- Git: 2.56


## 2) 수행 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성

<pre>
wsl --install -d Ubuntu-22.04  우분튜 설치
/mnt/c/WINDOWS/system32$ mkdir -p ~/codyssey/practice 
우분투 설치시 사용자를 codyssey 지정해서 디렉토리 생성시 /home/codyssey 하위에 생성
cd ~codyssey/codyssey/practice/
pwd
/home/codyssey/codyssey/practice

권한변경 실습을 위해 파일 chmod_manual 파일을 생성한다

echo 'aaasasasadasdsadsdasd' >> chmod_manual

</pre>

- [x] 권한 변경 실습

<pre>
가. 파일변환 문자 기준

대상 [ugoa]*: 권한을 바꿀 대상을 정합니다.
u (user): 파일 소유자
g (group): 그룹 사용자
o (others): 기타 사용자
a (all): 위 모두를 포함 (기본값)
연산자 [-+=]: 권한을 어떻게 처리할지 정합니다.
+: 권한 추가
-: 권한 제거
=: 지정한 권한으로 변경 (기존 권한 무시)
권한 [rwxXst]*: 구체적인 권한 종류입니다.
r (read): 읽기
w (write): 쓰기
x (execute): 실행
X: 대상이 디렉터리이거나 이미 실행 권한이 있는 경우에만 실행 권한 부여
s, t: 특수 권한 (SetUID, Sticky bit 등)

chmod u+x chmod_manual   # 소유자(u)에게 실행(x) 권한을 추가(+)합니다.
[결과]-rwxr--r-- 1 codyssey codyssey 6463 Oct 30 15:27 chmod_manual*
chmod g-w chmod_manual   # 그룹(g)에서 쓰기(w) 권한을 제거(-)합니다.
[결과]-rwxr-xrwx 1 codyssey codyssey 6463 Oct 30 15:27 chmod_manual*
chmod a=r chmod_manual   # 모든 사용자(a)의 권한을 읽기(r) 전용으로 설정(=)합니다.
[결과]-r--r--r-- 1 codyssey codyssey 6463 Oct 30 15:27 chmod_manual

나. 파일변환 숫자기준

[-+=][0-7]+ 부분에 해당합니다.

숫자 세 개를 조합하여 권한을 한 번에 지정하는 방식입니다. 각 숫자는 **읽기(4), 쓰기(2), 실행(1)**의 합으로 계산합니다.

숫자 계산법:
7 (4+2+1): 읽기+쓰기+실행
6 (4+2): 읽기+쓰기
5 (4+1): 읽기+실행
4: 읽기 전용

chmod 755 chmod_manual  # 소유자: 7(모든 권한), 그룹/기타: 5(읽기+실행)
[결과]-rwxr-xr-x 1 codyssey codyssey 6463 Oct 30 15:27 chmod_manual*
chmod 644 chmod_manual # 소유자: 6(읽기+쓰기), 그룹/기타: 4(읽기 전용)
[결과]-rw-r--r-- 1 codyssey codyssey 6463 Oct 30 15:27 chmod_manual

</pre>


     
- [x] Docker 설치/점검
<pre>
Docker  window amd 버전 설치 
docker --version
     
윈도우에서는 docker --version  버전이 나오는데 wsl(우분투) 에서는 안나옴

## 트러블 슈팅 : 윈도우에서는 docker 가 정상적으로 설치되었지만 우분투는 설치안되었다고 나옴</font>
## 원인 및 가설 : 윈도우에 설치 되었기 때문에 환경에 다른 우분투에서 안나오기 때문에 연결이 작업이 필요하다고 네이토가 답변함
## 해결 / 대안 : WSL integration > Enable integration with additional distros 에서 Ubuntu-22.04 선택 후 Apply & Restart Engine 클릭 후 정상확인됨

/mnt/c/WINDOWS/system32$ docker --version
Docker version 29.9.2, build a82e528
     
[네이터대답]
원인: Windows ↔ WSL은 별개 환경 → 연결(Integration) 필요
해결: Docker Desktop → Settings → Resources → WSL Integration → Ubuntu 토글 ON → Apply & Restart
(본인해결확인)WSL integration > Enable integration with additional distros 에서 Ubuntu-22.04 선택 후 Apply & Restart Engine 클릭
확인: WSL 터미널 새로 열고 docker --version

[에러 및 수정 후 결과]
docker --version

The command 'docker' could not be found in this WSL 2 distro.
We recommend to activate the WSL integration in Docker Desktop settings.

For details about using Docker Desktop with WSL 2, visit:

https://docs.docker.com/go/wsl2/

codyssey@4F-29:/mnt/c/WINDOWS/system32$ docker --version
Docker version 29.9.2, build a82e528

docker info 실행

오류발생 :(맨끝)permission denied while trying to connect to the docker API at unix:///var/run/docker.sock

## 트러블 슈팅 : 우분투에서 docker info 실행시 권한 오류 발생 
## (에러내용)permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
## 원인 및 가설 : /var/run$ ls -alF docker.sock
## srw-rw---- 1 root docker 0 Nov  3 14:26 docker.sock=   그룹에게만 write 권한을 주고 있는데 codyssey 는 같은 그룹이 아님
## 해결 / 대안 : 네이토 질의에도 그룹유저 권한이 지정하라고 나와서  sudo usermod -aG docker $USER 후 wsl 재시작 확인하니 정상처리 됨
</pre>
[네이토대답]
1. 내 계정을 docker 그룹에 추가
sudo usermod -aG docker $USER
2. 그룹 추가는 새 세션부터 적용돼요. 그래서 WSL을 완전히 재시작해야 해요.
exit로 터미널만 닫는 건 부족할 수 있어서, wsl --shutdown으로 완전히 껐다 켜는 걸 추천 
3단계: 확인
WSL 터미널을 다시 열고: 에러난거 다시 실행
     
docker info # 정상작동

- [x] hello-world 실행

<pre>
codyssey 유저에서 docker 실행시 codyssey 사용자에게 그룹 write 권한 주기전 까지는 권한오류가 발생하였으나 권한부여 후 아래처럼 정상 작동됨
 
<pre>
codyssey@4F-29:~/codyssey/practice$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
</pre>

- [x] Dockerfile 빌드/실행

① Dockerfile 작성  →  ② docker build (이미지 생성)  →  ③ docker run (컨테이너 실행)
   (설계도 작성)         (설계도로 완제품 틀 만들기)       (틀로 실제 가동!)

비유로 이해하기

Dockerfile = 요리 레시피 📝
이미지(Image) = 레시피로 만든 냉동식품 🧊 (언제든 데울 수 있는 완성품)
컨테이너(Container) = 냉동식품을 데워서 먹는 상태 🍽️ (실제 실행 중)

index.html 생성
<pre>
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>내 첫 Docker 웹페이지(Juno)</title>
    <style>
        body {
            font-family: sans-serif;
            text-align: center;
            margin-top: 100px;
            background-color: #f0f8ff;
        }
        h1 { color: #2496ed; }
    </style>
</head>
<body>
    <h1>🐳 Docker로 띄운 첫 웹페이지!(Juno)</h1>
    <p>축하합니다! 컨테이너가 잘 작동하고 있어요. 🎉</p>
</body>
</html>
</pre>
Dockerfile 생성
<pre>
# 1. Nginx 웹서버를 기반으로 시작
FROM nginx:latest

# 2. 내 HTML 파일을 Nginx의 기본 폴더로 복사
COPY index.html /usr/share/nginx/html/index.html

# 3. 80번 포트를 사용한다고 알림 (문서화 목적)
EXPOSE 80
</pre>

docker build -t 1st-web-app .
>>>  2026.11.2 에 할때는 성공하였으나 2026.11.3 애 할때는 build 오류 발생

## 트러블 슈팅 : 우분투에서 docker build 실행시 권한 오류 발생 
## (에러내용)ERROR: failed to build: failed to solve: error getting credentials - err: exec: "docker-credential-desktop.exe": executable file not found in $PATH, out: ``"
## 원인 및 가설 : 해당 PATH 에 실행할 파일이 없다고 나옴
## 네이토질의 결과 : 설정환경이 꼬일떄 발생된다고 나오고 해결방법은 config.json 에 있는 내용을 변경
## 해결 / 대안 : config.json 에 있는 내용을   "auths": { ... } 로 변경하니 정상작동 됨

docker images
                                                                                                    i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
1st-web-app:latest   432af8aa689a        241MB         63.9MB
hello-world:latest   c567b231f656       25.9kB         9.49kB    U

docker run -d -p 8080:80 1st-web-app

codyssey@4F-29:~/codyssey/1st-docker-app$ docker run -d -p 8080:80 1st-web-app
2f464073f6a3cde0cff66bf1bdd2e4818a5961ff30e71a3eebfd673374653d9f

<img width="1302" height="451" alt="image" src="https://github.com/user-attachments/assets/546d3074-de1b-4bfe-b429-97ccbdb2f1ea" />


- [x] 포트 매핑 접속(2회)

만약 포트를 바꾸고 싶다면, 실행 중인 컨테이너를 중지(docker stop)하고 삭제(docker rm)한 뒤, 새로운 포트 번호로 다시 run 해서 처
docker stop container_id ## stop 방법

docker run -d -p 8081:80 1st-web-app   >> http://localhost:8081 에 테스트 페이지 나옴
558d33b2f5730b599f641eb57c802d3faae431f0f7f253e520eb72fe96f5f623

<img width="1916" height="555" alt="image" src="https://github.com/user-attachments/assets/977c72f9-e181-4a1e-a88b-5608657c8472" />


docker run -d -p 8082:80 1st-web-app   >> http://localhost:8082 에 테스트 페이지 나옴
5daf4525acc01acbbe2c4e2940becc3cf386bb1c7ad817d6c67d473668f3ba8e

<img width="1911" height="457" alt="image" src="https://github.com/user-attachments/assets/fe0e91f8-723a-41a9-b095-6543cf278016" />



codyssey@4F-78:~$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                     NAMES
5daf4525acc0   1st-web-app   "/docker-entrypoint.…"   13 minutes ago   Up 13 minutes   0.0.0.0:8082->80/tcp, [::]:8082->80/tcp   nice_keller
558d33b2f573   1st-web-app   "/docker-entrypoint.…"   14 minutes ago   Up 14 minutes   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   upbeat_almeida



- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [x] Git 설정 + VSCode GitHub 연동


# 이름 설정
git config --global user.name "Juno"

# 이메일 설정 (GitHub 계정 이메일과 동일하게)
git config --global user.email "your-email@example.com" -- 설정안함

# 설정 확인
git config --list

Git 초기화: 터미널에 git init을 입력하여 현재 폴더를 Git 저장소로 만듭니다.
파일 상태 확인: git status를 입력하면 현재 추적되지 않은 파일들이 보입니다.

codyssey@4F-29:~/codyssey/1st-docker-app$ git init
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint:
hint:   git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint:   git branch -m <name>
Initialized empty Git repository in /home/codyssey/codyssey/1st-docker-app/.git/

git status  -- 저장된 파일들이 나온다./1st-docker-app/ 에 있는 

3. Docker 관련 주의사항 (.gitignore)
Docker를 사용하면 node_modules, 로그 파일, 혹은 빌드 결과물 등 GitHub에 올릴 필요가 없는 무거운 파일들이 생길 수 있습니다. 이를 제외하기 위해 .gitignore 파일을 만들어야 합니다.

<pre>
# Docker 관련 제외
.dockerignore
# 노드 패키지 (Node.js 사용 시)
node_modules/
# 빌드 결과물
dist/
# 환경변수 파일 (보안 중요!)
.env
</pre>

4. GitHub 원격 저장소 연결하기
GitHub에서 새로운 Repository(저장소)를 만듭니다.
생성된 저장소의 주소(HTTPS 주소)를 복사합니다.
VS Code 터미널에서 아래 명령어를 입력하여 연결합니다.
<pre>
# 원격 저장소 연결 (주소 부분에 복사한 URL 붙여넣기)
git remote add origin https://github.com/사용자이름/저장소이름.git
- joono0609
# 브랜치 이름을 main으로 변경 (최신 기본값)
git branch -M main
</pre>

5. 파일 올리기 (Add, Commit, Push)
이제 실제로 파일을 GitHub에 전송해 봅시다.

<pre>
     # 1. 변경된 모든 파일을 스테이징 영역에 추가
git add .

# 2. 버전 기록 (메시지는 짧고 명확하게)
git commit -m "Initial commit: Docker setup and project files"

# 3. GitHub로 전송
git push -u origin main
</pre>

Docker 파일: Dockerfile이나 docker-compose.yml 같은 설정 파일은 GitHub에 올라가야 다른 환경에서도 똑같이 실행할 수 있습니다.
VS Code 확장 프로그램: VS Code 왼쪽의 '소스 제어' 아이콘(세 번째 아이콘)을 클릭하면 명령어를 치지 않고도 마우스 클릭만으로 add, commit, push를 할 수 있어 편리합니다.


## 2) 수행 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습
- [x] Docker 설치/점검
- [x] hello-world 실행
- [x] Dockerfile 빌드/실행
- [x] 포트 매핑 접속(2회)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [x] Git 설정 + VSCode GitHub 연동

 ## 3) 수행 로그(발췌)
bash$ pwd
/home/user
$ mkdir -p ~/codyssey/practice
$ ls -la
...

