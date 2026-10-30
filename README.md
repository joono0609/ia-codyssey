# ia-codyssey
Github 와 codyssey 와 연동하기 위해 만들어진 Repository 입니다 


#프로젝트 개요(미션 목표 요약)
#실행 환경(OS/쉘/터미널, Docker 버전, Git 버전)
#수행 항목 체크리스트(터미널/권한/Docker/Dockerfile/포트/볼륨/Git/GitHub)
#검증 방법(어떤 명령으로 무엇을 확인했는지) + 결과 위치 링크
#트러블슈팅 2건 이상(문제 → 원인 가설 → 확인 → 해결/대안)7
#기술 문서만 읽어도 전체 수행 내용을 파악할 수 있어야 한다.


#프로젝트 개요(미션 목표 요약)

개발에 필요한 개발환경을 직접 설치 및 실제 작동함으로서 DevOps 환경에 대해 이해도가 생겨 업무 프로세스 자동화 및 혁신하는데 도움을 주고자 합니다.

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
- Docker: 26.x
- Git: 2.56


## 2) 수행 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습

wsl --install -d Ubuntu-22.04  우분튜 설치

codyssey@4F-61:~$ pwd
/home/codyssey

codyssey@4F-61:~$ mkdir ./practice
codyssey@4F-61:~$ ls -al
total 32
drwxr-x--- 4 codyssey codyssey 4096 Oct 30 15:23 .
drwxr-xr-x 3 root     root     4096 Oct 30 15:03 ..
-rw-r--r-- 1 codyssey codyssey  220 Oct 30 15:03 .bash_logout
-rw-r--r-- 1 codyssey codyssey 3771 Oct 30 15:03 .bashrc
drwx------ 2 codyssey codyssey 4096 Oct 30 15:03 .cache
-rw-r--r-- 1 codyssey codyssey  807 Oct 30 15:03 .profile
-rw------- 1 codyssey codyssey 1531 Oct 30 15:15 .viminfo
drwxr-xr-x 2 codyssey codyssey 4096 Oct 30 15:23 practice


     
- [x] Docker 설치/점검
- [x] hello-world 실행
- [x] Dockerfile 빌드/실행
- [x] 포트 매핑 접속(2회)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [x] Git 설정 + VSCode GitHub 연동



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

