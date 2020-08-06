**도커 이미지 빌드 및 도커허브 업로드**

### 1. 도커에 jdk 설치
1) ubuntu로 가상머신 생성

2) Putty 127.0.0.1:22 접속 후 root password를 kopo로 변경
    (sudo passwd root / kopo / kopo)
3) root계정으로 접속
(su -)
4) 패키지와 버전을 업데이트 
(apt-get update)
5) apt-get install libltdl-dev
6) 도커이미지 가져오기 
(wget https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/docker-ce_17.06.2~ce-0~ubuntu_amd64.deb)
7) 이미지 unpacking 
(dpkg -i docker-ce_17.06.2~ce-0~ubuntu_amd64.deb)
8) 도커 버전 확인 
(docker -,-version 쉼표 없이 붙여야함)
9) docker 응용 프로그램을 실행하기 위해 compose를 설치 
(curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose)
!* curl이 설치되어 있지 않은 경우는 apt install curl을 설치해준다
10) docker compose 설치 후 실행 권한 주기 
(chmod +x /usr/local/bin/docker-compose)
11) docker 버전을 확인 후 최신 우분투 이미지 당겨오기 
(docker pull ubuntu:latest)
12) 컨테이너에 접속 
(docker run -it --name myubuntu ubuntu:latest /bin/bash)	( --name myubuntu  어떤걸로 만들지 naming 해주는 것)
13) FileZilla에 호스트 연결 
(호스트:127.0.0.1, 사용자명: kopo, 비밀번호: kopo, 포트: 22)
14) 자바 파일을 docker의 kopo 폴더로 	전송
15) kopo폴더에 자바 파일이 	이동되었는지 	확인 
(컨테이너에 접속한 상태라면 ctrl+P,Q로 나온 후 cd /home/kopo로 이동 후 ll로 확인)
16) 자바 파일을 컨테이너 root 밑으로 	이동 
(docker cp  jdk-8u181-linux-x64.tar.gz ubuntu:/root)
17) 컨테이너에 접속 
(docker attach myubuntu)
18) 자바 파일 압축해제	
(tar xzfv jdk-8u181-linux-x64.tar.gz)
19) 압축해제한 파일을 /usr/local 밑으로 이동 
(mv jdk1.8.0_181 /usr/local)
20) 자바에 별칭 잡아주기 
(ln -s jdk1.8.0_181 java)
!* 압축해제 된 파일을 /usr/local 이동하려고 했지만 목적지에 같은 파일이 있어서 오류.
해결: 목적지 이동하여 폴더가 있는지 확인한 후 삭제 (cd /usr/local) -> (ll)-> (rm –rf jdk1.8.0_181)

21) jdk 환경 변수 설정 	
(vi /root/.bashrc) - > export JAVA_HOME=/usr/local/java	export PATH=$JAVA_HOME/bin/:$PATHexport CLASSPATH=“.”
22)  자바 버전 확인	
(javac –version)

### 2. Tomcat 설치

1) http://apache.tt.co.kr/tomcat/tomcat9/v9.0.36/bin/apache-tomcat-9.0.36.tar.gz (사이트 이동 시 저절로 파일 다운로드 됨)
2) FileZilla이용하여 톰켓 파일을 docker 폴더로 이동 
3) 톰켓 파일이 FileZilla로 이동이 되었는지 확인 후 도커 컨테이너에 톰켓 옮기기 
(cd /home/kopo) ->(ll) -> (docker cp apache-tomcat-9.0.36.tar.gz myubuntu-0.1:/root)
4) 컨테이너로 접속 후 파일 확인 
(docker attach myubuntu) -> (cd /root) -> (ll)
5) 톰켓 압축해제 후 파일 이동 후 별칭 지어주기 
(tar xzf apache-tomcat-9.0.36.tar.gz) -> (mv apache-tomcat-9.0.36 /usr/local) -> (ln -s apache-tomcat-9.0.36/ tomcat)

6) 톰켓 환경변수 설정 후 source /root/.bashrc 
(기존에 설정되어 있던 설정에 덮어 써준다) (vi /root/.bashrc) -> 
export JAVA_HOME=/usr/local/java 
export CATALINA_HOME=/usr/local/tomcat
export PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar:$CATALINA_HOME/lib/jsp-api.jar:$CATALINA_HOME/lib/servlet-api.jar
export LC_ALL=C.UTF-8

7) 톰켓 실행 
($CATALINA_HOME/bin/startup.sh)
8) 톰켓 정지 
($CATALINA_HOME/bin/shutdown.sh)


### 3. 컨테이너 -> 이미지

1) Docker login
2) Docker commit [컨테이너명] [새로만들 이미지명]:[버전명] 
    (docker commit myubuntu rnvl898)
3)  Docker tagging (docker tag kopo/ubuntu:0.1[도커아이디]/ubuntu:0.1) 
    (Docker tag rnvl898/kopo:0.1)
4)  도커 허브에 이미지 업로드 (docker push rnvl898/kopoL0.1)

!* 도커 허브에 push할 때 denied 오류 뜸
해결방법: Docker tagging 할 때 꼭 도커 아이디명이 들어가야함
*기존 명령 
(docker commit myubuntu kopo/ubuntu:0.1 -> docker tag kopo/ubuntu:0.1 rnvl898/ubuntu:0.1)
*개선 명령 
(docker commit myubuntu rnvl898 ->  docker tag rnvl898 rnvl898/kopo:0.1)


### 아래 주소는 ppt로 정리가 되어있습니다. 사진 첨부 및 트러블슈팅 내용이 포함되어있어 참고하시면 좋습니다. 
(https://github.com/paul7958/Python-Practice/blob/master/%EB%8F%84%EC%BB%A4%20%EB%B9%8C%EB%93%9C.pptx)



