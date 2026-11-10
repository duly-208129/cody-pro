# 개발 워크스테이션 구축 프로젝트

## 1. 프로젝트 소개

### 1-1. 프로젝트 목적

본 프로젝트는 <작업실 꾸미기 미션 실행>을 위해 Windows 환경에서 개발 워크스테이션을 구축하고, 터미널 명령어와 Linux 파일 권한, Docker 컨테이너 운영, Git 및 GitHub를 활용한
버전 관리 방법을 학습하기 위해 진행하였다.

단순히 개발 도구를 설치하는 것에 그치지 않고, Dockerfile을 이용하여 직접
웹 서버 이미지를 제작하고 컨테이너를 실행하였다. 또한 포트 매핑, 바인드
마운트, Docker Volume을 실습하여 컨테이너의 네트워크와 데이터 관리
원리를 확인하였다.

### 1-2. 주요 수행 내용

-   PowerShell을 이용한 파일 및 디렉터리 관리
-   Linux 환경에서 파일 및 디렉터리 권한 변경
-   Docker 설치 상태 및 엔진 동작 확인
-   Docker 이미지 다운로드 및 컨테이너 실행
-   Dockerfile을 이용한 NGINX 커스텀 이미지 제작
-   포트 매핑을 통한 웹 서버 접속
-   바인드 마운트를 이용한 호스트 파일 변경 반영
-   Docker Volume을 이용한 데이터 영속성 검증
-   Git 설정 및 GitHub 원격 저장소 연동
-   VS Code를 이용한 프로젝트 관리
-   문제 발생 원인 분석 및 해결 과정 기록

### 1-3. 최종 목표

Docker 기반 웹 서버를 직접 구축하고 운영할 수 있으며, 파일 권한과 데이터
영속성, 네트워크 포트 매핑, Git 버전 관리의 기본 원리를 이해하는 것을
목표로 한다.

------------------------------------------------------------------------

## 2. 개발 환경 및 사전 점검

### 2-1. 개발 환경

  구분                 사용 환경
  -------------------- --------------------
  운영체제             Windows 10 Home
  터미널               Windows PowerShell
  PowerShell           5.1
  Linux 환경           WSL2 / Ubuntu
  Docker               Docker Desktop
  Docker CLI           29.9.2
  Git                  2.56.0.windows.4
  코드 편집기          Visual Studio Code
  컨테이너 운영 환경   Linux / x86_64

> 위 버전은 기존 실습에서 확인한 기록이다. 제출 시점의 환경과 다르면
> 실제 확인 결과로 수정한다.

### 2-2. PowerShell 버전 확인

``` powershell
$PSVersionTable
```

기존 확인 기록:

``` text
PSVersion        5.1.19041.6456
PSEdition        Desktop
```

### 2-3. 운영체제 확인

``` powershell
systeminfo
```

기존 확인 기록:

``` text
OS Name: Microsoft Windows 10 Home
OS Version: 10.0.19045
System Type: x64-based PC
```

### 2-4. Git 버전 확인

``` powershell
git --version
```

기존 확인 기록:

``` text
git version 2.56.0.windows.4
```

### 2-5. Docker 버전 확인

``` powershell
docker --version
docker desktop version
```

기존 확인 기록:

``` text
Docker version 29.9.2, build a82e528
```

### 2-6. Docker 엔진 동작 상태 확인

``` powershell
docker info
```

주요 확인 항목:

``` text
Client:
 Version: 29.9.2
 Context: desktop-linux

Server:
 Server Version: 29.9.2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
```

-   Docker Client는 사용자의 명령어를 전달한다.
-   Docker Server는 이미지와 컨테이너를 실제로 관리한다.
-   `docker info`에서 Server 정보가 정상적으로 표시되어야 Docker 엔진과
    통신할 수 있다.
-   Client 버전만 표시되고 Server 연결 오류가 발생하면 Docker Desktop
    실행 상태를 확인한다.

------------------------------------------------------------------------

## 3. 프로젝트 디렉터리 구성

### 3-1. 디렉터리 생성 및 목록 확인

``` powershell
mkdir app
mkdir docker
mkdir docker-workstation
mkdir practice
mkdir logs
mkdir screenshots
mkdir images

Get-ChildItem -Force
```

### 3-2. 프로젝트 디렉터리 구조

``` text
my-first-Docker/
│
├── app/
│   └── index.html
│
├── images/
│   └── 폴더구조.PNG
│
├── logs/
│   └── terminal.md
│
├── screenshots/
│   └── [실습 증거 이미지]
│
├── docker/
│   └── [추가 Docker 실습 파일]
│
├── docker-workstation/
│   └── [호스트 데이터 실습 파일]
│
├── practice/
│   └── [터미널 실습 파일]
│
├── Dockerfile
├── .gitignore
├── practice.txt
└── README.md
```

> 위 구조는 최종 정리하고자 하는 내용이다. 실제 저장소에 없는 파일과 폴더는
> 생성한 후에 반영한다.

### 3-3. 디렉터리별 구성 목적

  디렉터리 및 파일 및 용도
  ------------------ --------------------------------
  `app/`             웹 서버에서 제공할 HTML 파일
  `images/`          README에 삽입할 이미지
  `logs/`            터미널 및 Docker 실행 기록
  `screenshots/`     실습 과정 증거 이미지
  `docker/`          추가 Docker 실습 및 설정 파일
  `docker-workstation/`     호스트 데이터 실습
  `practice/`        파일 및 디렉터리 관리 실습
  `Dockerfile`       커스텀 Docker 이미지 제작
  `.gitignore`       Git에 등록하지 않을 파일 지정
  `README.md`        프로젝트 수행 과정과 결과 기록

기능별로 파일을 분리하여 관리하면 파일의 위치와 역할을 쉽게 파악할 수
있고, 향후 기능을 추가할 때도 관리하기 편리하다.

### 3-4. 프로젝트 구조 이미지

![프로젝트 폴더 구조](images/폴더구조.PNG)

------------------------------------------------------------------------

## 4. 터미널 기본 명령어 실습

### 4-1. 현재 작업 위치 확인

``` powershell
Get-Location
pwd
```

현재 PowerShell에서 작업 중인 디렉터리의 절대 경로를 확인한다.

### 4-2. 디렉터리 목록 확인

``` powershell
Get-ChildItem
Get-ChildItem -Force
```

-   `Get-ChildItem`: 일반 파일 및 디렉터리 목록 확인
-   `Get-ChildItem -Force`: 숨김 파일과 디렉터리를 포함한 목록 확인

### 4-3. 디렉터리 이동

``` powershell
cd .\app
Get-Location

cd ..
Get-Location
```

`cd` 명령어를 이용하여 하위 디렉터리와 상위 디렉터리로 이동한다.

### 4-4. 파일 및 디렉터리 생성

``` powershell
mkdir test-folder

New-Item practice.txt -ItemType File

Set-Content practice.txt "안녕하세요 ? 작업실 꾸미기 Docker 실습입니다."

Get-Content practice.txt
```

예상 확인 내용:

``` text
안녕하세요 ? 작업실 꾸미기 Docker 실습입니다.
```

### 4-5. 파일 복사 및 이름 변경

``` powershell
Copy-Item practice.txt practice-copy.txt

Rename-Item practice-copy.txt renamed-file.txt

Get-ChildItem
```

### 4-6. 파일 삭제

``` powershell
Remove-Item renamed-file.txt

Test-Path .\renamed-file.txt
```

정상적으로 삭제되었다면 `False`가 출력된다.

### 4-7. 절대 경로와 상대 경로

절대 경로는 드라이브 또는 파일시스템의 루트부터 시작하는 전체 경로이다.

예시:

``` text
C:\Users\사용자\k'\app\index.html
```

상대 경로는 현재 작업 디렉터리를 기준으로 표현하는 경로이다.

예시:

``` text
.\app\index.html
```

  구분   절대 경로                             상대 경로
  ------ ------------------------------------- -------------------------
  기준   드라이브 또는 루트                    현재 작업 위치
  특징   위치가 명확함                         짧고 간결함
  활용   작업 위치와 무관하게 특정 파일 접근   프로젝트 내부 파일 접근

프로젝트 내부 파일을 참조할 때는 상대 경로를 사용하고, 특정 위치를
명확하게 지정해야 할 때는 절대 경로를 사용하는 것이 편리하다.

### 4-8. 터미널 실습 결과 기록

주요 명령어 실행 결과는 `logs/terminal.md`에 별도로 정리한다.

``` powershell
Get-Location
Get-ChildItem -Force
Get-Content .\practice.txt
Test-Path .\renamed-file.txt
```

실제 실행한 명령어와 출력 결과를 기록하고, 필요한 경우 터미널 스크린샷을
추가한다.

------------------------------------------------------------------------

## 5. Linux 파일 및 디렉터리 권한 실습

### 5-1. WSL 설치 상태 확인

PowerShell에서 실행한다.

``` powershell
wsl --list --verbose
```

기존 확인 기록:

``` text
NAME              STATE           VERSION
docker-desktop    Stopped         2
Ubuntu            Stopped         2
```

### 5-2. Ubuntu 실행

``` powershell
wsl -d Ubuntu
```

Ubuntu에 진입한 후 실행한다.

``` bash
pwd
df -T .
```

WSL의 Linux 전용 파일시스템 환경에서 권한 실습을 수행한다.

### 5-3. 파일 생성 및 초기 권한 확인

``` bash
mkdir -p ~/mission-practice
cd ~/mission-practice
touch test.txt
ls -l test.txt
```

기존 확인 기록:

``` text
-rw-r--r-- 1 사용자 사용자 0 test.txt
```

### 5-4. 파일 권한 변경

600 권한으로 변경:

``` bash
chmod 600 test.txt
ls -l test.txt
```

기존 확인 기록:

``` text
-rw------- 1 사용자 사용자 0 test.txt
```

700 권한으로 변경:

``` bash
chmod 700 test.txt
ls -l test.txt
```

기존 확인 기록:

``` text
-rwx------ 1 사용자 사용자 0 test.txt
```

### 5-5. 디렉터리 권한 변경

``` bash
mkdir -p permission-test
ls -ld permission-test

chmod 755 permission-test
ls -ld permission-test

chmod 700 permission-test
ls -ld permission-test
```

기존 755 확인 기록:

``` text
drwxr-xr-x 2 사용자 사용자 4096 permission-test
```

기존 700 확인 기록:

``` text
drwx------ 2 사용자 사용자 4096 permission-test
```

### 5-6. 파일 권한 숫자 표기의 원리

Linux 권한은 소유자(User), 그룹(Group), 기타 사용자(Other)의 세 영역으로
구분된다.

  권한     숫자 의미
  ------ ------ -----------
  r           4 읽기
  w           2 쓰기
  x           1 실행
  \-          0 권한 없음

755의 의미:

``` text
755 = 7 / 5 / 5

소유자: 4 + 2 + 1 = 7 (rwx)
그룹:   4 + 1     = 5 (r-x)
기타:   4 + 1     = 5 (r-x)
```

644의 의미:

``` text
644 = 6 / 4 / 4

소유자: 4 + 2 = 6 (rw-)
그룹:   4     = 4 (r--)
기타:   4     = 4 (r--)
```

대표적인 사용 사례:

-   755: 디렉터리 또는 실행 가능한 스크립트
-   644: 일반 문서 및 HTML 파일
-   700: 소유자만 접근해야 하는 디렉터리
-   600: 소유자만 읽고 쓸 수 있는 파일

### 5-7. 권한 실습 종료

``` bash
exit
```

권한 변경 전후 결과는 터미널 로그 또는 스크린샷으로 기록한다.

------------------------------------------------------------------------

## 6. Docker 기본 명령어 및 컨테이너 실습

### 6-1. Docker 엔진 확인

``` powershell
docker --version
docker info
```

Docker Desktop이 실행 중인지 확인한 후 실습을 진행한다.

### 6-2. hello-world 컨테이너 실행

``` powershell
docker run hello-world
```

기존 실행 기록:

``` text
Hello from Docker!

This message shows that your installation appears to be working correctly.
```

실행 과정:

1.  Docker Hub에서 `hello-world` 이미지를 다운로드한다.
2.  다운로드한 이미지로 컨테이너를 생성한다.
3.  컨테이너가 실행되면서 정상 메시지를 출력한다.

### 6-3. Ubuntu 컨테이너 실행

``` powershell
docker run -it --name mission-ubuntu ubuntu:latest bash
```

컨테이너 내부에서 실행한다.

``` bash
pwd
ls -la
echo "Docker 실습 성공 !!!"
touch test.txt
echo "Ubuntu Container 실습" > test.txt
cat test.txt
```

기존 실행 기록:

``` text
Docker 실습 성공 !!!

Ubuntu Container 실습
```

### 6-4. 컨테이너 종료 및 재진입

컨테이너 내부에서 종료한다.

``` bash
exit
```

PowerShell에서 상태를 확인한다.

``` powershell
docker ps
docker ps -a
```

컨테이너 재실행 및 진입:

``` powershell
docker start mission-ubuntu
docker exec -it mission-ubuntu bash
```

파일 확인:

``` bash
cat test.txt
```

기존 확인 기록:

``` text
Ubuntu Container 실습
```

### 6-5. attach와 exec의 차이

  명령어            역할
  ----------------- ---------------------------------------------
  `docker exec`     실행 중인 컨테이너에서 새로운 프로세스 실행
  `docker attach`   컨테이너의 기존 주 프로세스에 연결
  `docker start`    중지된 컨테이너 다시 실행
  `docker stop`     실행 중인 컨테이너 중지
  `docker rm`       컨테이너 삭제

컨테이너에서 연결만 해제하려면 `Ctrl + P`를 누른 뒤 `Ctrl + Q`를
입력한다. 이 방법은 컨테이너의 주 프로세스를 종료하지 않고 터미널 연결만
해제한다.

### 6-6. Docker 이미지 및 컨테이너 목록 확인

``` powershell
docker images
docker ps
docker ps -a
```

-   `docker images`: 로컬에 저장된 이미지 확인
-   `docker ps`: 실행 중인 컨테이너 확인
-   `docker ps -a`: 종료된 컨테이너까지 전체 확인

기존 실습에서 확인한 이미지:

``` text
hello-world:latest
ubuntu:latest
```

### 6-7. Docker 로그 확인

``` powershell
docker logs mission-ubuntu
```

컨테이너에서 출력된 메시지와 실행 기록을 확인한다.

### 6-8. Docker 리소스 사용량 확인

``` powershell
docker run -d --name stats-test nginx:latest
docker stats --no-stream
```

기존 확인 기록:

``` text
CONTAINER ID   NAME        CPU %   MEM USAGE / LIMIT
2a4dda1fb87b   stats-test  0.00%   15.95MiB / 3.755GiB
```

실습 후 정리:

``` powershell
docker stop stats-test
docker rm stats-test
```

### 6-9. Docker 이미지와 컨테이너의 차이

  -----------------------------------------------------------------------
  구분                    이미지                  컨테이너
  ----------------------- ----------------------- -----------------------
  역할                    실행 환경의 템플릿      이미지로 생성한 실행
                                                  환경

  생성                    Dockerfile을 빌드       이미지를 실행

  변경                    빌드 과정을 통해 새로운 컨테이너 내부에서 변경
                          이미지 생성             가능

  삭제                    `docker rmi`            `docker rm`
  -----------------------------------------------------------------------

이미지는 컨테이너를 생성하기 위한 기반이고, 컨테이너는 이미지로부터
생성되어 실제 프로그램을 실행하는 환경이다.

컨테이너 내부의 일반 쓰기 계층에 저장한 데이터는 컨테이너 삭제 시 사라질
수 있으므로, 유지해야 하는 데이터는 Volume이나 Bind Mount에 저장해야
한다.

------------------------------------------------------------------------

## 7. Dockerfile을 이용한 커스텀 웹 서버 구축

### 7-1. 베이스 이미지 선정

기존 NGINX 이미지를 활용하여 커스텀 웹 서버를 제작한다.

  구분            내용
  --------------- -------------------
  베이스 이미지   `nginx:latest`
  커스텀 이미지   `my-webserver:v1`
  웹 서버         NGINX
  웹 콘텐츠       `app/index.html`
  컨테이너 포트   80
  호스트 포트     8080

### 7-2. 웹 페이지 소스 작성

파일 위치:

``` text
app/index.html
```

소스 코드:

``` html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>Hello, Docker!</h1>
    <p>내 첫 번째 커스텀 웹 서버입니다.</p>
    <p>Development Workstation Mission</p>
</body>
</html>
```

### 7-3. Dockerfile 작성

파일 위치:

``` text
Dockerfile
```

내용:

``` dockerfile
FROM nginx:latest

COPY app/index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

  명령어     목적
  ---------- --------------------------------------
  `FROM`     기존 NGINX 이미지를 기반으로 사용
  `COPY`     HTML 파일을 컨테이너 내부에 복사
  `EXPOSE`   컨테이너가 사용하는 포트 80을 문서화

기존 웹 서버 이미지의 기능을 활용하고 웹 콘텐츠를 교체하는 방식으로
커스텀 이미지를 제작한다.

> 실제 빌드에 사용하는 Dockerfile은 프로젝트 루트의 `Dockerfile`이다.
> `docker/Dockerfile`과 혼동하지 않도록 관리한다.

### 7-4. 커스텀 이미지 빌드

``` powershell
docker build -t my-webserver:v1 -f .\Dockerfile .
```

기존 빌드 기록:

``` text
[+] Building FINISHED

[1/2] FROM docker.io/library/nginx:latest

[2/2] COPY app/index.html /usr/share/nginx/html/index.html

naming to docker.io/library/my-webserver:v1
```

옵션 설명:

-   `docker build`: 이미지 빌드
-   `-t my-webserver:v1`: 이미지 이름과 태그 지정
-   `-f .\Dockerfile`: 사용할 Dockerfile 지정
-   마지막 `.`: 현재 디렉터리를 빌드 컨텍스트로 지정

### 7-5. 이미지 생성 확인

``` powershell
docker images
```

기존 확인 이미지:

``` text
my-webserver:v1
nginx:latest
```

### 7-6. 웹 서버 컨테이너 실행

``` powershell
docker run -d --name my-webserver-container -p 8080:80 my-webserver:v1
```

  구분              포트
  --------------- ------
  호스트 포트       8080
  컨테이너 포트       80

### 7-7. 포트 매핑 원리

Docker 컨테이너는 호스트와 분리된 네트워크 환경을 사용한다. 따라서
컨테이너 내부의 80번 포트에서 실행 중인 웹 서버에 호스트에서 접근하려면
포트 매핑이 필요하다.

``` text
사용자 브라우저
      |
      v
localhost:8080
      |
      v
Docker 포트 포워딩
      |
      v
컨테이너 내부 네트워크
      |
      v
NGINX :80
```

`-p 8080:80`은 호스트의 8080번 포트로 들어온 요청을 컨테이너의 80번
포트로 전달한다.

`EXPOSE 80`만으로 호스트 포트가 공개되는 것은 아니다. 실제 포트 연결은
`-p` 옵션으로 설정한다.

### 7-8. 웹 서버 접속 검증

``` powershell
curl.exe -i http://localhost:8080
```

브라우저 주소:

``` text
http://localhost:8080
```

정상 응답 예시:

``` text
HTTP/1.1 200 OK

<h1>Hello, Docker!</h1>
<p>내 첫 번째 커스텀 웹 서버입니다.</p>
```

**\[추가 검증 필요\]**

기존 README에는 접속 성공 기록이 있으나 이후 연결 거부 오류도
발생했으므로 현재 실행 상태에서 다시 검증한다.

확보할 증거:

-   `docker ps`의 포트 매핑 결과
-   `curl.exe -i`의 HTTP 200 응답
-   브라우저 주소창에 `http://localhost:8080`이 표시된 화면

저장 위치:

``` text
images/webserver-access.png
```

![웹 서버 접속 결과](images/webserver-access.png)

------------------------------------------------------------------------

## 8. 바인드 마운트 실습

### 8-1. 바인드 마운트의 목적

바인드 마운트는 Windows 호스트의 특정 디렉터리를 컨테이너 내부
디렉터리에 연결하는 기능이다.

호스트의 HTML 파일을 수정하면 컨테이너 내부 웹 서버가 해당 변경 내용을
바로 읽을 수 있다.

### 8-2. 기존 컨테이너 정리

``` powershell
docker ps -a
docker stop mission-bind
docker rm mission-bind
```

컨테이너가 존재하지 않는 경우 오류가 발생할 수 있으므로 먼저 목록을
확인한다.

### 8-3. 바인드 마운트 컨테이너 실행

PowerShell에서 실행한다.

``` powershell
$AppPath = (Resolve-Path .\app).Path

docker run -d --name mission-bind -p 8080:80 --mount "type=bind,source=$AppPath,target=/usr/share/nginx/html,readonly" nginx:latest
```

  항목          내용
  ------------- ---------------------------------------------------
  `type=bind`   바인드 마운트 방식
  `source`      Windows 호스트의 app 디렉터리
  `target`      컨테이너 내부 NGINX 웹 콘텐츠 디렉터리
  `readonly`    컨테이너에서 호스트 파일을 수정하지 못하도록 설정

### 8-4. 호스트 파일 변경 전 확인

``` powershell
Get-Content .\app\index.html
curl.exe http://localhost:8080
```

기존 HTML 내용이 출력되는지 확인한다.

### 8-5. 호스트 파일 변경

``` powershell
Set-Content .\app\index.html '<h1>Hello, Bind Mount!</h1>'
```

변경 후 확인:

``` powershell
Get-Content .\app\index.html
curl.exe http://localhost:8080
```

정상적으로 연결되었다면 컨테이너를 다시 빌드하거나 생성하지 않아도
변경된 HTML 내용이 표시되어야 한다.

**\[추가 검증 필요\]**

호스트 파일 변경 전과 변경 후의 결과를 비교할 수 있는 증거를 확보한다.

``` text
images/bind-mount-before.png
images/bind-mount-after.png
```

### 8-6. 바인드 마운트의 특징

-   호스트와 컨테이너가 동일한 파일을 공유한다.
-   호스트 파일을 수정하면 컨테이너에 변경 내용이 반영된다.
-   소스코드 개발 및 웹 페이지 수정에 적합하다.
-   `readonly` 설정 시 컨테이너에서 호스트 파일을 수정할 수 없다.

------------------------------------------------------------------------

## 9. Docker Volume을 이용한 데이터 영속성 검증

### 9-1. Docker Volume 생성

``` powershell
docker volume create mission-data
```

기존 실행 기록:

``` text
mission-data
```

### 9-2. Volume 연결 및 데이터 저장

``` powershell
docker run -it --name volume-test -v mission-data:/data alpine sh
```

컨테이너 내부에서 실행한다.

``` sh
echo "Docker Volume Persistence Test" > /data/test.txt
cat /data/test.txt
exit
```

기존 실행 기록:

``` text
Docker Volume Persistence Test
```

### 9-3. 컨테이너 삭제 전 데이터 확인

``` powershell
docker run --rm -v mission-data:/data alpine cat /data/test.txt
```

기존 확인 기록:

``` text
Docker Volume Persistence Test
```

### 9-4. 기존 컨테이너 삭제

``` powershell
docker rm volume-test
```

기존 실행 기록:

``` text
volume-test
```

### 9-5. 컨테이너 삭제 후 데이터 확인

새로운 컨테이너를 생성하여 동일한 Volume을 연결한다.

``` powershell
docker run --rm -v mission-data:/data alpine cat /data/test.txt
```

기존 확인 기록:

``` text
Docker Volume Persistence Test
```

기존 컨테이너를 삭제했음에도 데이터가 유지되는 것을 확인하였다.

### 9-6. Volume 상세 정보 확인

``` powershell
docker volume inspect mission-data
docker volume ls
```

확인 항목:

-   Volume 이름
-   Driver
-   Mountpoint

### 9-7. 바인드 마운트와 Docker Volume 비교

  구분            바인드 마운트            Docker Volume
  --------------- ------------------------ ------------------------
  저장 위치       호스트에서 지정한 경로   Docker가 관리하는 공간
  호스트 경로     직접 지정                별도 경로 지정 불필요
  주요 목적       파일 공유                데이터 영속성
  대표 활용       웹 소스코드              데이터베이스 데이터
  컨테이너 삭제   호스트 파일 유지         Volume 데이터 유지

### 9-8. Docker Volume 백업

Volume의 데이터 영속성과 백업은 서로 다른 개념이다.

Volume은 컨테이너 삭제로부터 데이터를 보호하지만, Volume 자체가
삭제되거나 손상되는 경우를 대비하려면 별도의 백업이 필요하다.

백업 디렉터리 생성:

``` powershell
New-Item -ItemType Directory -Force "$HOME\mission-backups"
```

백업 파일명 생성:

``` powershell
$backup_file = "mission-data-$(Get-Date -Format 'yyyyMMdd-HHmmss').tar.gz"
```

Docker Volume 백업:

``` powershell
docker run --rm --mount "type=volume,source=mission-data,target=/data,readonly" --mount "type=bind,source=$HOME\mission-backups,target=/backup" alpine sh -c "tar -czf /backup/$backup_file -C /data ."
```

백업 파일 확인:

``` powershell
Get-ChildItem "$HOME\mission-backups"
```

**\[추가 검증 필요\]**

실제 백업 파일 생성 결과와 파일 크기를 확인한 후 증거를 기록한다.

### 9-9. Docker Volume 복원

복원용 Volume 생성:

``` powershell
$restore_volume = "mission-data-restore-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
docker volume create $restore_volume
```

백업 데이터 복원:

``` powershell
docker run --rm --mount "type=volume,source=$restore_volume,target=/data" --mount "type=bind,source=$HOME\mission-backups,target=/backup,readonly" alpine sh -c "tar -xzf /backup/$backup_file -C /data"
```

복원 결과 확인:

``` powershell
docker run --rm --mount "type=volume,source=$restore_volume,target=/data,readonly" alpine ls -l /data

docker run --rm --mount "type=volume,source=$restore_volume,target=/data,readonly" alpine cat /data/test.txt
```

복원된 데이터가 기존 데이터와 일치하는지 확인한다.

백업 파일은 GitHub 공개 저장소에 올리지 않는다.

------------------------------------------------------------------------

## 10. Git 설정 및 GitHub 연동

### 10-1. Git 사용자 정보 설정

``` powershell
git config --global user.name "Git 사용자 이름"
git config --global user.email "Git 등록 이메일"
```

개인 이메일을 공개 README에 직접 기록하지 않는다.

### 10-2. 기본 브랜치 설정

``` powershell
git config --global init.defaultBranch main
git branch --show-current
```

기본 브랜치를 `main`으로 설정한다.

### 10-3. Git 설정 확인

``` powershell
git config --list
```

개인정보를 제외한 주요 설정을 확인한다.

``` powershell
git config --global --get init.defaultBranch
git config --global --get core.autocrlf
```

사용자 이름과 이메일은 실제 설정값을 그대로 공개하지 않도록 주의한다.

### 10-4. Git 저장소 상태 확인

``` powershell
git status
git branch --show-current
git remote -v
```

### 10-5. GitHub 원격 저장소 연결

저장소 주소:

``` text
https://github.com/ksdevl/my-first-Docker.git
```

원격 저장소가 이미 등록되어 있다면 중복으로 추가하지 않고 주소를
확인하거나 수정한다.

``` powershell
git remote set-url origin https://github.com/ksdevl/my-first-Docker.git
```

### 10-6. 변경사항 커밋 및 업로드

``` powershell
git add .
git status
git commit -m "Complete development workstation mission"
git push origin main
```

### 10-7. GitHub 업로드 검증

``` powershell
git log -1 --oneline
git rev-parse HEAD
git ls-remote origin refs/heads/main
```

-   `git log -1 --oneline`: 최근 커밋 확인
-   `git rev-parse HEAD`: 현재 로컬 커밋 해시 확인
-   `git ls-remote origin refs/heads/main`: GitHub 원격 main 브랜치 커밋
    확인

로컬 커밋 해시와 원격 브랜치 커밋 해시가 일치하면 해당 커밋이 원격
저장소에 반영된 것이다.

### 10-8. VS Code와 GitHub 연동

연동 절차:

1.  VS Code 실행
2.  프로젝트 폴더 열기
3.  계정 메뉴에서 GitHub 로그인
4.  Source Control 메뉴에서 Git 저장소 확인
5.  원격 저장소 연결 여부 확인
6.  파일 수정 후 변경사항 확인
7.  Commit 및 Push 기능 확인

**\[추가 검증 필요\]**

다음 증거를 확보한다.

-   VS Code에서 GitHub 로그인 및 계정 연동 화면
-   Source Control에서 변경사항 및 원격 저장소가 확인되는 화면

저장 위치:

``` text
images/vscode-github.png
images/vscode-source-control.png
```

인증 토큰, 비밀번호, 이메일 주소, 인증 코드 등이 화면에 노출되지 않도록
주의한다.

------------------------------------------------------------------------

## 11. 트러블슈팅

### 11-1. Git 원격 저장소 연결 및 Push 오류

**문제**

``` text
error: remote origin already exists.
```

또는

``` text
! [rejected] main -> main (fetch first)
```

**원인 가설**

-   로컬 저장소에 origin이라는 원격 저장소가 이미 등록되어 있었다.
-   로컬과 원격 저장소의 커밋 이력이 서로 달랐다.

**확인**

``` powershell
git remote -v
git status
git log --oneline --all
```

**해결**

기존 원격 저장소 주소 확인 및 수정:

``` powershell
git remote set-url origin https://github.com/ksdevl/my-first-Docker.git
```

원격 변경사항 가져오기:

``` powershell
git pull origin main --allow-unrelated-histories
```

README 충돌 발생 시 내용을 비교하여 필요한 부분을 선택하고 충돌을
해결한다.

``` powershell
git add README.md
git commit -m "Resolve README merge conflict"
git push origin main
```

**해결 결과**

기존 실습에서 README 병합 충돌을 해결하고 원격 저장소 업로드에
성공하였다.

### 11-2. README 이미지가 표시되지 않는 문제

**문제**

README 미리보기에서 이미지가 표시되지 않고 Markdown 문법만 나타났다.

**원인 가설**

-   이미지 경로가 실제 파일 위치와 일치하지 않았다.
-   이미지 파일의 대소문자 또는 확장자가 일치하지 않았다.
-   이미지 Markdown 문법에 불필요한 백틱이 포함되었다.

**확인**

``` powershell
Test-Path ".\images\폴더구조.PNG"
Get-ChildItem .\images
```

**해결**

잘못된 작성:

``` text
`![프로젝트 폴더 구조](images/폴더구조.PNG)`
```

올바른 작성:

``` markdown
![프로젝트 폴더 구조](images/폴더구조.PNG)
```

**해결 결과**

불필요한 백틱을 제거하고 정확한 이미지 경로를 사용하여 이미지가
정상적으로 표시되는 것을 확인하였다.

### 11-3. Docker 웹 서버 포트 충돌

**문제**

``` text
Bind for 0.0.0.0:8080 failed:
port is already allocated
```

**원인 가설**

-   다른 Docker 컨테이너가 이미 8080번 포트를 사용하고 있다.
-   Windows의 다른 프로그램이나 서비스가 8080번 포트를 사용하고 있다.

**1단계: Docker 컨테이너 확인**

``` powershell
docker ps
docker ps --filter "publish=8080"
```

**2단계: Windows 시스템에서 포트 사용 프로세스 확인**

``` powershell
Get-NetTCPConnection -LocalPort 8080 -State Listen -ErrorAction SilentlyContinue |
Select-Object LocalAddress, LocalPort, State, OwningProcess
```

PID 확인:

``` powershell
Get-NetTCPConnection -LocalPort 8080 -State Listen -ErrorAction SilentlyContinue |
Select-Object -ExpandProperty OwningProcess -Unique
```

프로세스 정보 확인:

``` powershell
Get-Process -Id [PID]
```

`[PID]`에는 실제 확인한 프로세스 ID를 입력한다.

**3단계: Docker 컨테이너가 원인인 경우**

``` powershell
docker stop mission-bind
docker rm mission-bind
```

**4단계: 다른 프로그램이 원인인 경우**

프로세스 이름과 사용 목적을 먼저 확인한다. 종료해도 문제가 없는
프로그램임을 확인한 경우에만 종료한다.

``` powershell
Stop-Process -Id [PID]
```

**5단계: 다른 호스트 포트 사용**

``` powershell
docker run -d --name my-webserver-container -p 8081:80 my-webserver:v1
```

접속 주소:

``` text
http://localhost:8081
```

**6단계: 최종 확인**

``` powershell
docker ps
docker port my-webserver-container
curl.exe -i http://localhost:8081
```

**해결 결과**

실제 포트 사용 프로세스와 컨테이너를 확인한 후 정상 접속 여부를
기록한다.

### 11-4. Docker Desktop 엔진 연결 오류

**문제**

``` text
Cannot connect to the Docker daemon
```

또는

``` text
npipe:////./pipe/dockerDesktopLinuxEngine
```

**원인 가설**

-   Docker Desktop이 실행되지 않았다.
-   Linux 엔진이 정상적으로 시작되지 않았다.
-   Docker Context가 잘못 설정되었다.

**확인**

``` powershell
docker context ls
docker context show
docker info
```

**해결**

1.  Docker Desktop을 실행한다.
2.  Docker Engine이 정상적으로 시작될 때까지 기다린다.
3.  현재 Context를 확인한다.
4.  필요한 경우 Docker Desktop의 Linux 엔진 Context를 선택한다.

``` powershell
docker context use desktop-linux
docker info
```

**해결 결과**

`docker info`에서 Server 정보가 정상적으로 표시되는지 확인한다.

------------------------------------------------------------------------

## 12. 최종 검증

### 12-1. 터미널 최종 검증

``` powershell
Get-Location
Get-ChildItem -Force
Get-Content .\app\index.html
git status
```

### 12-2. Docker 최종 검증

``` powershell
docker --version
docker info
docker images
docker ps
docker ps -a
docker volume ls
docker volume inspect mission-data
```

### 12-3. 웹 서버 최종 검증

``` powershell
docker ps
docker port my-webserver-container
curl.exe -i http://localhost:8080
```

정상 응답이라면 HTTP 200 상태 코드가 표시되어야 한다.

### 12-4. 볼륨 영속성 최종 검증

``` powershell
docker run --rm -v mission-data:/data alpine cat /data/test.txt
```

기존에 저장한 데이터가 출력되는지 확인한다.

### 12-5. Git 및 GitHub 최종 검증

``` powershell
git status
git log -1 --oneline
git rev-parse HEAD
git ls-remote origin refs/heads/main
```

로컬과 원격 커밋 해시가 일치하는지 확인한다.

### 12-6. 최종 체크리스트

-   [x] Windows PowerShell 기본 명령어 실습
-   [x] 파일 및 디렉터리 생성·복사·이동·삭제
-   [x] 절대 경로와 상대 경로 이해
-   [x] Linux 파일 권한 변경 실습
-   [x] 파일 권한 600 및 700 변경
-   [x] 디렉터리 권한 755 및 700 변경
-   [x] Docker 버전 및 엔진 점검 기록
-   [x] hello-world 컨테이너 실행
-   [x] Ubuntu 컨테이너 실행 및 내부 명령어 수행
-   [x] Docker 이미지 및 컨테이너 관리
-   [x] Docker logs 및 stats 실행
-   [x] NGINX 기반 커스텀 이미지 빌드
-   [x] Dockerfile 기반 웹 서버 컨테이너 실행 기록
-   [ ] 현재 포트 매핑 접속 성공 증거 확보
-   [ ] 바인드 마운트 변경 전후 증거 확보
-   [x] Docker Volume 생성 및 연결
-   [x] 컨테이너 삭제 후 데이터 유지 확인 기록
-   [ ] Volume 백업 파일 생성 및 복원 검증 증거 보강
-   [x] Git 설정 및 GitHub Push 기록
-   [ ] VS Code와 GitHub 연동 스크린샷 확보
-   [x] Git 충돌 및 README 이미지 문제 해결 기록
-   [ ] 포트 사용 프로세스 확인 및 해결 결과 기록
-   [ ] 개인정보 및 보안 최종 검증

> 체크리스트는 문서 작성 시점의 증거 확보 상태를 반영한다. 추가 검증이
> 완료되면 체크 상태를 변경한다.

------------------------------------------------------------------------

## 13. 보안 및 개인정보 보호

### 13-1. 민감정보 확인

GitHub에 공개되는 문서와 로그에서 다음 항목을 확인한다.

-   개인 이메일 주소
-   Windows 사용자 이름 및 개인 경로
-   GitHub 인증 토큰
-   비밀번호
-   개인키 및 인증 코드
-   기타 계정 식별 정보

### 13-2. Git 사용자 정보 마스킹

Git 설정 확인 시 개인 이메일이 출력될 수 있으므로 README에는 실제 주소를
기록하지 않는다.

마스킹 예시:

``` text
user.name=*****
user.email=*****@*****
```

Git 로그에서도 개인정보가 노출되지 않도록 주의한다.

``` powershell
git log -5 --format="%h [작성자 마스킹]"
```

### 13-3. GitHub 저장소 보안 점검

`.gitignore`에 다음 항목을 포함한다.

``` gitignore
.env
.env.*
*.pem
*.key
*.p12
*.pfx
secrets/
credentials/
```

이미 Git에 등록된 민감정보는 `.gitignore`만으로 제거되지 않으므로 별도로
확인해야 한다.

### 13-4. 최종 보안 점검

-   [ ] README에 실제 이메일 주소가 없는지 확인
-   [ ] 터미널 로그에 토큰 및 비밀번호가 없는지 확인
-   [ ] 스크린샷에 인증 정보가 없는지 확인
-   [ ] GitHub에 개인키 및 인증 파일이 업로드되지 않았는지 확인
-   [ ] 불필요한 개인 경로 및 계정 정보 마스킹
-   [ ] 민감정보가 노출되었다면 해당 인증정보 폐기 및 재발급
-   [ ] GitHub 저장소 공개 범위 및 제출 권한 확인

------------------------------------------------------------------------

## 14. 학습 결과 및 프로젝트 결론

이번 개발 워크스테이션 구축 과제를 통해 Windows PowerShell과 Linux
터미널의 기본적인 사용법을 익혔으며, 파일과 디렉터리의 관리 및 권한 변경
방법을 실습하였다.

Docker에서는 이미지와 컨테이너의 차이를 이해하고, 기존 NGINX 이미지를
기반으로 Dockerfile을 작성하여 커스텀 웹 서버를 제작하였다.

또한 포트 매핑을 통해 호스트와 컨테이너 간 통신이 이루어지는 원리를
학습하였으며, 바인드 마운트와 Docker Volume을 이용하여 데이터 공유 및
영속성 관리 방법을 확인하였다.

Git과 GitHub를 연동하여 프로젝트 파일과 수행 기록을 관리하였고, 저장소
충돌 및 Docker 포트 문제 등 실제 작업 중 발생한 문제를 분석하고 해결하는
경험을 쌓았다.

앞으로는 Docker Compose, 컨테이너 네트워크, 이미지 최적화 등 추가적인
기술을 학습하여 보다 안정적인 개발 환경을 구축할 계획이다.

------------------------------------------------------------------------

## 15. 제출 저장소 및 증거자료

GitHub Repository:

https://github.com/ksdevl/my-first-Docker

주요 산출물:

  ------------------------------------------------------------------------
  산출물                              파일 위치
  ----------------------------------- ------------------------------------
  프로젝트 기술 문서                  `README.md`

  웹 서버 소스                        `app/index.html`

  Docker 이미지 설정                  `Dockerfile`

  터미널 실행 로그                    `logs/terminal.md`

  폴더 구조 이미지                    `images/폴더구조.PNG`

  웹 서버 접속 증거                   `images/webserver-access.png`

  바인드 마운트 증거                  `images/bind-mount-before.png`,
                                      `images/bind-mount-after.png`

  Docker Volume 증거                  `images/volume-persistence.png`

  VS Code 연동 증거                   `images/vscode-github.png`,
                                      `images/vscode-source-control.png`
  ------------------------------------------------------------------------

모든 결과물은 GitHub 저장소에서 확인할 수 있도록 관리한다.
