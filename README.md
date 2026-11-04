#프로젝트 개요

이 프로젝트는 리눅스(우분투)을 이용해 파일과 권한을 조작하고 Docker desktop 으로 직접 작성한 Dockerfile 으로  NGINX 웹서버 이미지를 제작하여 콘테이너를 실행하며 포트 매핑으로 웹페이지에 접속하고 바인드 마운트로 원본 파일 변경하면 웹페이지가 바로 반영되며 Docker 볼륨을 통해 컨테이너 삭제 후에도 데이터가 유지됨을 검증하고 Git과 GitHub로 수행 결과가 반영되는 실습을 위한 프로젝트이다.

## 1) 실행 환경
- OS: Ubuntu 22.04     
- Shell: bash
- Docker: 29.9.2
- Git: 2.56

## 우분투 설치
(script)wsl --install -d Ubuntu-22.04  우분튜 설치

(결과)
<img width="1216" height="370" alt="image" src="https://github.com/user-attachments/assets/d79dee14-1da4-4b46-b654-545d1357a7ff" />


## 우분투 Docker.Desktop , Git , Vscode 설치

(script) winget install --id Docker.DockerDesktop -e --accept-source-agreements --accept-package-agreements; 
winget install --id Git.Git -e --accept-source-agreements --accept-package-agreements; 
winget install --id Microsoft.VisualStudioCode -e --accept-source-agreements --accept-package-agreements

(결과)
<img width="1542" height="837" alt="image" src="https://github.com/user-attachments/assets/737c4d2b-ee8d-41d7-81f7-cdaed3143e40" />

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

# 트러블 슈팅 : 윈도우에서는 docker 가 정상적으로 설치되었지만 우분투는 설치안되었다고 나옴</font>
# 원인 및 가설 : 윈도우에 설치 되었기 때문에 환경에 다른 우분투에서 안나오기 때문에 연결이 작업이 필요하다고 네이토가 답변함
# 해결 / 대안 : WSL integration > Enable integration with additional distros 에서 Ubuntu-22.04 선택 후 Apply & Restart Engine 클릭 후 정상확인됨

codyssey@4F-76:~/codyssey/practice$ docker --version
Docker version 29.9.2, build a82e528

docker info 실행

오류발생 :(맨끝)permission denied while trying to connect to the docker API at unix:///var/run/docker.sock

# 트러블 슈팅 : 우분투에서 docker info 실행시 권한 오류 발생 
# (에러내용)permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
# 원인 및 가설 : /var/run$ ls -alF docker.sock
# srw-rw---- 1 root docker 0 Nov  3 14:26 docker.sock=   그룹에게만 write 권한을 주고 있는데 codyssey 는 같은 그룹이 아님
# 해결 / 대안 : 네이토 질의에도 그룹유저 권한이 지정하라고 나와서  sudo usermod -aG docker $USER 후 wsl 재시작 확인하니 정상처리 됨
</pre>
<pre>
1. sudo usermod -aG docker $USER  // 내 계정을 docker 그룹에 추가
2. 그룹 추가는 새 세션부터 적용되므로 WSL 재시작
* exit로 터미널만 닫는 건 부족할 수 있어서, wsl --shutdown 종료 
3. WSL 터미널을 다시 열고: 에러난거 다시 실행
     
docker info # 정상작동
[info.log] 참고
</pre>
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

<pre>
codyssey@4F-78:~$ docker ps
<img width="1685" height="635" alt="image" src="https://github.com/user-attachments/assets/abf94083-837e-43e6-b89b-823dcd6db3a7" />
</pre>

- [x] 바인드 마운트 반영

바인드 마운트(Bind Mount)는 도커 학습에서 가장 신기하고 편리한 기능으로 쉽게 말해 "내 컴퓨터의 폴더와 컨테이너 안의 폴더를 실시간으로 연결(동기화)하는 것 입니다.
<pre>
cd /home/codyssey/codyssey/project/html

echo '[HTML작성]' >> index.html
<img width="423" height="25" alt="image" src="https://github.com/user-attachments/assets/71bfae76-8e28-4f0d-8492-ea48d01d11ce" />


docker run -d -p 8085:80 -v $(pwd):/usr/share/nginx/html --name mount1-test 1st-web-app
</pre>
<img width="1916" height="227" alt="image" src="https://github.com/user-attachments/assets/7b1ac4f5-9e4f-4690-8129-426cfd9eb49f" />
<pre>
echo "[HTML수정]" >> index.html  -- 파일만 수정 후 http://localhost:8085 에서 다시 확인하여 내용이 변경되었나 확인
<img width="423" height="25" alt="image" src="https://github.com/user-attachments/assets/2f7d1eac-f584-4ef7-be43-7f5aadca02d2" />

</pre>
<img width="1912" height="292" alt="image" src="https://github.com/user-attachments/assets/a70fce47-26cc-4901-9f6c-947a83593597" />


- [x] 볼륨 영속성

<pre>
도커에서 영속성(Persistence)이라는 말은 아주 중요합니다. 컨테이너가 삭제되어도 데이터는 사라지지 않고 유지되는 성질을 뜻합니다.
도커 컨테이너는 기본적으로 '쓰고 버리는' 일회용품 같은 존재입니다. 컨테이너를 삭제하면 그 안에서 생성했던 파일이나 데이터베이스의 
데이터도 모두 함께 사라지죠. 이를 방지하기 위해 사용하는 것이 바로 볼륨(Volume)과 바인드 마운트(Bind Mount)입니다.

- 영속성을 유지하는 두 가지 방법
① 도커 볼륨 (Docker Volumes) - 도커가 관리
도커가 내 컴퓨터의 특정 구역을 알아서 관리하는 방식입니다. 가장 권장되는 방식입니다.
특징: 데이터가 어디에 저장되는지 사용자가 신경 쓸 필요가 없습니다. 도커가 안전하게 관리합니다.
장점: 도커 명령어로 관리하기 쉽고, 백업이나 이동이 편리합니다.

# 1. 볼륨 생성
docker volume create my-db-data
# 2. 볼륨을 컨테이너에 연결해서 실행
docker run -d -v my-db-data:/var/lib/mysql mysql

② 바인드 마운트 (Bind Mounts) - 사용자가 관리
지난번에 실습하신 것처럼, 내 컴퓨터의 특정 폴더 경로를 직접 지정해서 연결하는 방식입니다.
특징: 내가 눈으로 볼 수 있는 폴더(C:\project 등)를 직접 연결합니다.
장점: 소스 코드를 수정하고 바로 반영해야 하는 개발 환경에서 매우 유리합니다.

docker run -d -v /home/user/html:/usr/share/nginx/html nginx
</pre>
<pre>
(1) 볼륨을 사용해 컨테이너 실행 
docker run -d -v my-data:/data --name test alpine tail -f /dev/null
(2) 컨테이너 안에 파일 만들기
docker exec test touch /data/hello.txt
(3) 컨테이너 삭제
docker rm -f test
(4) 새 컨테이너를 같은 볼륨으로 실행
docker run -d -v my-data:/data --name test2 alpine tail -f /dev/null
(5) 파일 확인: docker exec test2 ls /data 
[결과]hello.txt가 그대로 남아있다면 성공
</pre>

<img width="1880" height="223" alt="image" src="https://github.com/user-attachments/assets/5e960238-05f7-4a6b-bf34-fbd98e6f0556" />


- [x] Git 설정 + VSCode GitHub 연동


# 이름 설정
git config --global user.name "joono0609"

# 이메일 설정 (GitHub 계정 이메일과 동일하게)
git config --global user.email "leesj69@gmail.com" 

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
git remote add origin https://github.com/joono0609/ia-codyssey.git
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
-- 이메일 지정하고 다시 하니 아래 화면이 나옴

<img width="1537" height="502" alt="image" src="https://github.com/user-attachments/assets/215f1f6e-b6fb-43de-8c28-d430f7b05325" />

     
# 3. GitHub로 전송
git push -u origin main

codyssey@4F-76:~/codyssey$ git push -u origin main
Username for 'https://github.com': leesj69@gmail.com
Password for 'https://leesj69%40gmail.com@github.com':
remote: Invalid username or token. Password authentication is not supported for Git operations.
fatal: Authentication failed for 'https://github.com/joono0609/ia-codyssey.git/'



</pre>

Docker 파일: Dockerfile이나 docker-compose.yml 같은 설정 파일은 GitHub에 올라가야 다른 환경에서도 똑같이 실행할 수 있습니다.
VS Code 확장 프로그램: VS Code 왼쪽의 '소스 제어' 아이콘(세 번째 아이콘)을 클릭하면 명령어를 치지 않고도 마우스 클릭만으로 add, commit, push를 할 수 있어 편리합니다.

