**리눅스 환경에 아나콘다 개발환경 구축 가이드**

준비항목: 가상머신(virtual Box), 
                운영체제(centOS), 
                분석서버(Anaconda), 
                터미널 연결 툴(putty), 
                파일전송 툴(FileZilla)



### 1-1. Linux 설치 (virtual Box)
1) 가상머신 이름 설정
2) 메모리 크기 2G정도 (2048)
3) 하드 설정
4) 파일 위치 및 크기 32G정도

### 1-2. 네트워크 설정
1) 어탭터1 - 어탭터에 브리지 -> 이름: 공유기로 설정 -> 무작위 모드: 모두 허용
2) 어탭터2 - 호스트 전용 어탭터 -> 이름: 호스트 전용 어탭터 -> 무작위 모드: 모두 허용

### 1-3. 저장소 설정
1) 설정 -> 저장소 -> 컨트롤러 : IDE 디스크에 광학 드라이브를 CentOS로 설정


### 1-4. 가상머신 실행
1) 소프트웨어 선택 -> 기본환경: 서버-GUI사용 체크
2) 설치대상 -> 체크


### 1-5. ROOT설정
1) root암호 설정
2) 사용자 생성

### 2-1. 계정 생성 (서버에 리눅스OS 설치 이후)
1) su - root (root로 접속 해야 계정 생성 가능
2) adduser '아이디'  (pydata)
3) passwd '아이디'   (pydata)
4 )비밀번호 설정

### 2-2. 계정 생성한 곳에 sudo권한 부여
1) su - root
2) visudo (권한 부여 하는 명령어 툴)
3) Allow root to run any commands anywhere 밑에
    root    ALL=(ALL)    ALL  밑에
    conda  ALL=(ALL)    ALL 입력  (권한을 부여할 아이디를 입력 후 ALL로 수행 가능한 명령어)
   *(모든 명령어 Passwd 스킵하려면 NOPASSWD:ALL 해줘야 함 / 일부 명령어는 NOPASSWD:root/test.sh)
   *(vi편집기에서 검색 기능은 '/' 후 검색할 키워드 ex. /Allow)

### 3-1. 서버 내 고정 IP 할당
1) 해당 가상 머신 설정 -> 네트워크 -> 어탭터에 브리지의 MAC주소 확인
2) nmcli (1번에서 확인한 MAC주소를 찾는다 or 연결되지 않은 네트워크를 찾음)
3) vi /etc/sysconfig/network-scripts/ifcfg-enp0s3 명령어를 친다. (enp0s3은 1번의 MAC주소와 동일한 서버)
4) BOOTPROTO = "static" 으로 변경 / ONBOOT = "yes" 로 변경 / IPADDR = 192.168. 110.113 / NETMAST = 255.255.255.0 / GATEWAY = 192.168.110.254
   *(ip주소,서브넷,게이트웨이 정보는 cmd에서 ipconfig로 확인)
   *(192.168.110. [*113*] 부분은 학교에서 할당받은 주소임 113~116까지)

////
### 3-2. 외부인터넷 연결을 위한 네임서버 등록
1) vi /etc/resolv.conf
2) nameserver 8.8.8.8 입력
3) 설정 완료를 반영하기 위해 systemctl restart network 입력 (네트워크 재시작)
4) ping www.google.com 핑 확인
////

### 4-1. 서버 본체의 Host Name을 설정
1) vi /etc/sysconfig/network
2) NETWORKING=yes
    HOSTNAME=conda (pymaster)입력


### 5-1. 서버에 연결될 서버들을 인식하기 위해 hosts를 설정.
1) vi /etc/hosts
2) 192.168.0.113 pymaster 


### 6-1. Selinux 해제
1) sestatus로 selinux의 상태를 확인 (ex. enabled)
2) vi /etc/selinux/config
   

### 7-1. 방화벽 해제
1) firewall-cmd --pernanent --zone=public --add-port=50070/tcp     ->     (포트 추가)
2) firewall-cmd --reload            ->         (방화벽 재시작)
3) firewall-cmd --list-all          ->     (사용 가능한 모든 서비스/포트 목록 조회)
4) firewall-cmd --list-ports       ->     (허용한 포트 목록)
5) firewall-cmd -state         ->     (방화벽 상태 확인)
6) systemctl stop firewalld       ->     (방화벽 기능 정지)
7) systemctl disable firewalld     ->    (방화벽 기능 해제)
![image](https://user-images.githubusercontent.com/57342451/89499452-00e51e00-d7fb-11ea-8682-e4297619432e.png)
![image](https://user-images.githubusercontent.com/57342451/89499498-178b7500-d7fb-11ea-88e5-08bbb9f6bf66.png)


### 8-1. putty 접속


### 9-1. 아나콘다 설치파일 다운로드
1) 아나콘다 다운로드 사이트 접속 및 링크 확인
![image](https://user-images.githubusercontent.com/57342451/89503843-1742a800-d802-11ea-84d7-882d88045246.png)


### 9-2. 아나콘다 설치파일 다운로드
생성한 계정으로 접속 후 홈 디렉토리에 파일 다운로드
1) su - pydata
2) cd /home/pydata
3) wget 다운로드 링크 (링크 복사한 주소 그대로 붙혀넣기)

![image](https://user-images.githubusercontent.com/57342451/89503881-23c70080-d802-11ea-928c-926c8c1e4a77.png)


### 9-3. 아나콘다 설치
설치파일 실행 및 라이센스 동의 (200번 정도 엔터 쳐야함)
1) bash Anaconda3- 2020.02-Linux-x86_64.sh

### 9-4. 아나콘다 설치
1) 설치 시 환경변수 등록 여부 yes
![image](https://user-images.githubusercontent.com/57342451/89503910-32151c80-d802-11ea-858a-ff5445cf0c55.png)


### 9-5. 아나콘다 설치
환경변수 자동 설정했지만 
1) 등록된 변수 확인 :  vi .bashrc
2) 환경변수 활성화 : soruce .bashrc

### 10-1. jupyter lab 설정
1) jupyter lab설정 : jupyter lab --generate-config

### 10-2. jupyter lab 설정
외부접속 허용(기존 로컬에서만 접속가능)
1) 127.0.0.1 -> 0.0.0.0(외부허용) 또는 서버 ip

![image](https://user-images.githubusercontent.com/57342451/89503944-3fcaa200-d802-11ea-94b5-66ff9a252d51.png)

![image](https://user-images.githubusercontent.com/57342451/89503955-42c59280-d802-11ea-948b-b0beac79108a.png)

### 10-3. jupyter lab 설정
초기 디렉토리 설정
1) 기본 .jupyter -> 원하는 관리 디렉토리로 변경 

![image](https://user-images.githubusercontent.com/57342451/89504012-5b35ad00-d802-11ea-989a-b0d822fe336d.png)

![image](https://user-images.githubusercontent.com/57342451/89504021-5ec93400-d802-11ea-8324-c4417511ed0b.png)

### 10-4. jupyter lab 설정
브라우저 속성 설정: open_browser
1) open_browser = True -> False로 변경

### 11-1. jupyter lab 실행 및 보안설정
1) 외부에서 실행한 서버 접속 주소로 들어간다 (token포함)
 (로컬주소:포트번호/lab)

![image](https://user-images.githubusercontent.com/57342451/89504074-71436d80-d802-11ea-95c3-7471c59df0b8.png)

### 11-2. jupyter lab 실행 및 보안설정
1) 보안설정

![image](https://user-images.githubusercontent.com/57342451/89504113-7e605c80-d802-11ea-92f9-d70b84363afb.png)

### 11-3. jupyter lab 실행 및 보안설정
1) 보안설정 notebookapp.password : (입력)

![image](https://user-images.githubusercontent.com/57342451/89504132-86200100-d802-11ea-8c12-c92f0076d365.png)


### 12-1. 개발환경 구축완료
1) 주소만 입력하면 비밀번호 입력 후 접속 가능
(로컬주소:포트번호)
![image](https://user-images.githubusercontent.com/57342451/89504159-8ddfa580-d802-11ea-80fb-1af060dc91f4.png)


**_설명에서 나오는 로컬주소와 사진에 나오는 로컬주소는 다를 수 있음. (사진에 나오는 로컬주소는 무시해도 됨)_**
