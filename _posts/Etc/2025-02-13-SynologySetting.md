---
layout: single
title: Synology Setting 
categories: synology
tag: [etc, synology]

toc: true
toc_sticky: true
---


<div class="notice--danger" markdown="1">
**<u>주의사항</u>** 

기본적으로 셋팅을 맞는 방법으로 했다 하더라도 NAS 자체에 버그가 많아서 제대로 적용안될 수도 있으니 감안하고 작업
</div>


## 1. DSM 설치

DSM이란 Synology의 운영체제를 말한다.
NAS를 전원연결 및 내부 LAN에 연결 뒤
[https://finds.synology.com/](https://finds.synology.com/)
해당 주소로 들어가서 설치하면 됨
   
   
## 2. quickconnect 설정

쉽게 원격을 킬 수 있게 해주는 설정
설치 중에 Synology 계정을 만들라고 하고 이에 파생해서 설정할 수 있다
나중에 DDNS셋팅하면 자동으로 DDNS로 바뀐다.
   
   
## 3. 포트포워딩

NAS에 접속하기 위해서는 우선 포트포워딩으로 포트를 열어줘야 함
   
### 3-1. 공유기 포트포워딩
가지고 있는 공유기마다 셋팅이 다르다.
여기서는 KT homehub를 기준으로 설명
   
참고로 공유기 설정은 기본게이트웨이 ip를 치고 들어가면 됨

* 80 : 인증서 인증을 위함
* 443 : 기본 웹 접속을 위한 포트
* 5000 : synology 기본 http 포트
* 5001 : synology 기본 https 포트
* 21 : ftp 포트

gitea 포트
* 3052
* 2222

SVN 포트
* 2690
* 2443
* 2080

<div class="notice--info" markdown="1">
**<u>공유기에 들어오는 IP가 외부 IP가 아닐때</u>** 

어떤 공유기들은 외부IP에서 들어오는 게 아니라 내부 IP로 변환된 IP 주소가 들어온다
이 때 해결방법은
1. 브릿지모드
2. Super DMZ
</div>

   
   
## 4. DDNS 셋팅(외부접속)

Synology에서 DDSN를 셋팅해줘서 외부에서 접속할 때 URL을 IP 주소가 아닌 영어로 칠 수 있게 셋팅

### 4-1. DDNS 셋팅
1. 제어판 > 외부 액세스 > DDNS 에서 추가 버튼선택
2. Synology 1개당 하나의 도메인을 무료로 제공해주므로 서비스 공급자는 Synology로 선택

### 4-2. 역방향 프록시 셋팅
역방향프록시를 셋팅해주면 synology에 접속할 때 뒤에 붙여야 했던 포트번호를 생략할 수 있다.

<div class="notice--info" markdown="1">
**<u>예시</u>** 

포트번호와 앞의 DDNS앞에 문자열 지정가능
https://example.synology.me:5001 >> https://dsm.example.synology.me
</div>

제어판 > 로그인포털 > 고급 > 역방향 프록시 > 생성 선택
* 역방향 프록시 이름 : 원하는 이름 선택. 여기서는 dsm으로 설정
* 소스 : 외부에서 들어올 주소를 등록
	* 프로토콜 : https
	* 호스트이름 : DDNS 주소 앞에 원하는 문자열을 추가하는 형식으로 추가
	* 포트 : 소스의 포트므로 https는 무조건 443으로 지정
* 대상 : 내부에서 반응할 주소를 등록
	* 프로토콜 : 위와 동일하게 https
	* 호스트이름 : NAS 내부 서비스이기 때문에 localhost (NAS 내부 IP)
	* 포트 : 띄워줄 시스템의 지정된 포트(여기서는 DSM의 포트인 5001을 지정 )

### 4-3. 인증서 셋팅

<div class="notice--warning" markdown="1">
**<u>인증서셋팅을 가장 마지막에 하는 것이 오류가 적다</u>** 

역방향프록시 설정 이후에 인증서셋팅
</div>

인증서를 셋팅하지 않으면 synology에 접속할 때마다 보안 알람이 표시된다.
1. 제어판 > 보안 > 인증서 탭에서 추가
2. 새 인증서 추가 > Let's Encrypt에서 인증서 얻기
3. 도메인 이름에 위에서 셋팅해준 DDNS 이름을 적고 이메일을 기입해서 완료
4. 필수!!) 주제대체이름에 와일드카드 도메인을 써줘야 하위도메인까지 전부 인증 가능
5. "4-1. DDNS" 셋팅 에 있는 기본 인증서로 설정 하는 체크박스를 true로 해서 적용

   
## 5. Portainer 설치(gitea 1단계)

### 5-1. Container Manager 설치(Docker 설치)
패키지 센터에서 Container Manager 설치
![Image](https://github.com/user-attachments/assets/989c798a-4f44-45e1-b573-0ca8f39bb7df)

이렇게 2개 설치해주면 됨
   
### 5-2. 파일스테이션에 폴더 생성
![Image](https://github.com/user-attachments/assets/0230ce73-123e-42c9-8c93-9dfe91712110)

portainer 폴더 생성
   
### 5-3. 스크립트 생성
제어판 > 작업스케쥴러 > 생성 > 예약된작업 > 사용자 정의 스크립트
![Image](https://github.com/user-attachments/assets/cbff4f93-0439-45ea-8be8-ebef6be456fe)
   
![Image](https://github.com/user-attachments/assets/82b119eb-a407-49ff-8b83-0185b14f1847)

![Image](https://github.com/user-attachments/assets/0cc0a9c1-98f1-4c10-99fc-56a9b5393660)
![Image](https://github.com/user-attachments/assets/aa1337ae-91a7-4240-947c-1fae4814ee6f)

일반, 스케쥴, 작업설정 참고
사용자 정의 스크립트는 다음과 같이 붙여넣음
```
docker run -d --name=portainer \ 
-p 8000:8000 \ 
-p 9000:9000 \ 
-v /var/run/docker.sock:/var/run/docker.sock \ 
-v /volume1/docker/portainer:/data \ 
--restart=always \ 
portainer/portainer-ce
```
완료하면 경고창은 무시하고 비밀번호를 입력
이 뒤에 스크립트 실행
   
### 5-4. portainer 셋팅
1. http://172.30.1.2:9000/ 로 접속(NAS IP : 9000)
2. 사용자 계정 생성
3. Get Started > 연필모양 아이콘 > public IP 입력(NAS Local IP)
![Image](https://github.com/user-attachments/assets/cd06f855-0e67-4317-9318-d5dec6f78cd7)
![Image](https://github.com/user-attachments/assets/3574a424-4eae-4621-94d9-8689342e8642)
![Image](https://github.com/user-attachments/assets/bce0699c-4087-4ef4-8ac7-17f17c4db94d)

4. Registries > Custom registry 클릭 후 이미지와 같이 추가
![Image](https://github.com/user-attachments/assets/7a4b5a6b-1d59-4ecb-8083-2dc62fcd1559)
![Image](https://github.com/user-attachments/assets/b84d25e4-a33d-4ac9-9188-fa2debc3d1c9)

5. 최종확인
![Image](https://github.com/user-attachments/assets/5df1492f-3e26-4729-a5ab-d9e44210fffe)


## 6. gitea 설치

**<u>UID와 GID 얻는 법</u>**
{: .notice--primary} 

1. 메일로 스크립트가 오게 하려면 미리 셋팅이 필요. 알림 > 이메일
![Image](https://github.com/user-attachments/assets/de8f4986-0168-4ccf-ba98-a33264bb8f82)

이 쪽에서 셋팅하면 됨
   
2. 제어판 > 작업스케쥴러 > 생성 > 예약된작업 > 사용자 정의 스크립트
![Image](https://github.com/user-attachments/assets/5ba5ac63-5015-47da-89d7-3a947372e30f)

```
id
```
스크립트는 id라고만 하면 됨

### 6-1. Git Server 설치
![Image](https://github.com/user-attachments/assets/9cdd6a86-6c90-443a-88a5-6fc249c04c39)

패키지 센터에서 Git Server 설치

![Image](https://github.com/user-attachments/assets/37ea3350-d146-4e45-b14c-253225b29b98)

터미널 및 SNMP > SSH 서비스활성화 체크 후 적용
   
### 6-2. 텍스트 편집기 설치
이 작업을 하기 위해서 텍스트 편집기를 설치하면 편리하다

![Image](https://github.com/user-attachments/assets/95bee7df-55da-4103-8109-db60d54be0c9)

패키지센터 > 텍스트 편집기 설치
   
### 6-3. 역방향 프록시 셋팅
![Image](https://github.com/user-attachments/assets/09c34bda-8ec1-4e54-bb95-b494e7ae81fb)

DDNS입력 포트는 3052로 입력

**<u>Destination 의 Protocol은 HTTP</u>** 
{: .notice--danger}

사용자 지정헤더 > Websocket 생성
   
### 6-4. 폴더 생성
![Image](https://github.com/user-attachments/assets/327787f1-b2b7-4438-8e55-bca7d4c271a2)

gitea폴더를 만들고 그 안에 data와 db 폴더를 전부 소문자로 생성
   
### 6-5. Portainer에서 스택추가
![Image](https://github.com/user-attachments/assets/86c221b3-c369-4a06-9272-945147c4fa29)

이후 Web Editor에 다음 내용 복사

```
version: "3.9"
services:
  db:
    image: postgres
    container_name: Gitea-DB
    hostname: gitea-db
    security_opt:
      - no-new-privileges:true
    healthcheck:
      test: ["CMD", "pg_isready", "-q", "-d", "gitea", "-U", "giteauser"]
      timeout: 45s
      interval: 10s
      retries: 10
    user: 1026:100
    volumes:
      - /volume1/docker/gitea/db:/var/lib/postgresql/data:rw
    environment:
      - POSTGRES_DB=gitea
      - POSTGRES_USER=giteauser
      - POSTGRES_PASSWORD=giteapass
    restart: on-failure:5

  web:
    image: gitea/gitea:latest
    container_name: Gitea
    hostname: gitea
    security_opt:
      - no-new-privileges:true
    healthcheck:
      test: wget --no-verbose --tries=1 --spider http://localhost:3000/ || exit 1
    ports:
      - 3052:3000
      - 2222:22
    volumes:
      - /volume1/docker/gitea/data:/data
      - /etc/TZ:/etc/TZ:ro
      - /etc/localtime:/etc/localtime:ro
    environment:
      - USER_UID=1026
      - USER_GID=100
      - GITEA__database__DB_TYPE=postgres
      - GITEA__database__HOST=gitea-db:5432
      - GITEA__database__NAME=gitea
      - GITEA__database__USER=giteauser
      - GITEA__database__PASSWD=giteapass
      - ROOT_URL=https://gitea.example.synology.me
    restart: on-failure:5
```
user: 1026:100  > 이 부분은 UID와 GID 를 넣으면 됨
USER_UID=1026 > UID
USER_GID=100 > GID
ROOT_URL=https://gitea.example.synology.me > 역방향 프록시에서 셋팅한 값 기입

Deploy the stack 실행하면 스택이 실행되면서 gitea가 설치됨
   
### 6-6. gitea 초기셋팅
위에서 셋팅한 URL로 들어간 뒤

![Image](https://github.com/user-attachments/assets/d696ec20-1853-43c4-b7ed-525390c4b034)

여기서는 변경하지 않고 그냥 넘어가면 됨

서버 도메인을 다시한번 셋팅해주고 Install Gitea를 선택

(버튼이 2개 있을텐데 가장 아래에 있는 것을 선택해주면 되는 듯)

10분정도 기다리면 설치가 완료되면서 gitea 메인화면이 표시됨


Register를 선택해서 원하는 아이디를 만들면 됨
   
## 6-7. gitea 가입 막는 법
해당 경로에 설정 파일들이 있는데 
```
/volume1/docker/gitea/data/gitea/conf/app.ini
```
그 중 DISABLE_REGISTRATION 의 값을 true로 설정하면 됨

### \[참고링크]
* portainer 설치 : [https://mariushosting.com/synology-30-second-portainer-install-using-task-scheduler-docker/](https://mariushosting.com/synology-30-second-portainer-install-using-task-scheduler-docker/)
* gitea 설치 : [https://mariushosting.com/how-to-install-gitea-on-your-synology-nas/](https://mariushosting.com/how-to-install-gitea-on-your-synology-nas/)
* gitea 설정값 : [https://docs.gitea.com/next/administration/config-cheat-sheet](https://docs.gitea.com/next/administration/config-cheat-sheet)
* UID/GID : [https://mariushosting.com/synology-find-uid-userid-and-gid-groupid-in-5-seconds/](https://mariushosting.com/synology-find-uid-userid-and-gid-groupid-in-5-seconds/)
* KT공유기 포트포워딩 : [https://atnbt.com/%EC%BC%80%EC%9D%B4%ED%8B%B0-%EA%B3%B5%EC%9C%A0%EA%B8%B0-%ED%8F%AC%ED%8A%B8%ED%8F%AC%EC%9B%8C%EB%94%A9/](https://atnbt.com/%EC%BC%80%EC%9D%B4%ED%8B%B0-%EA%B3%B5%EC%9C%A0%EA%B8%B0-%ED%8F%AC%ED%8A%B8%ED%8F%AC%EC%9B%8C%EB%94%A9/)
* Let's Encrypt 인증서 : [https://www.bearpooh.com/33](https://www.bearpooh.com/33)
* Let's Encrypt 와일드카드 : [https://mariushosting.com/synology-how-to-add-wildcard-certificate/](https://mariushosting.com/synology-how-to-add-wildcard-certificate/)
* Synology DDNS : [https://youtu.be/pHQaOqkAo48](https://youtu.be/pHQaOqkAo48)


## 7. SVN 셋팅

### 7-1. Contrainer Manager(Docker)에서 svn 설치
![Image](https://github.com/user-attachments/assets/7d7bf384-c354-45ac-889b-d4283a376fd8)

elleflorio것을 설치해준다

![Image](https://github.com/user-attachments/assets/6cef4785-1326-437e-9c9e-e4e838ba1e1b)

포트는 다음과 같이 설정

![Image](https://github.com/user-attachments/assets/cba8cce6-8198-49c3-be76-a61b3089ac92)

역방향 프록시는 다음과 같이 설정
   
### 7-2. SVN 셋팅
![Image](https://github.com/user-attachments/assets/826a5d71-96a3-41ee-b2c4-ef366e4eeb10)
옵션들을 다음과 같이 바꿔준다

| 옵션                                                     | 경로                                      |
| -------------------------------------------------------- | ----------------------------------------- |
| **Subversion authorization file**                        | /etc/subversion/subversion-access-control |
| **User authentication file (SVNUserFile)**               | /etc/subversion/passwd                    |
| **Parent directory of the repositories (SVNParentPath)** | /home/svn                                 |
| **Subversion client executable**                         | /usr/bin/svn                              |
| **Subversion admin executable**                          | /usr/bin/svnadmin                         |


[초기 계정]
```
ID : admin
Pass : admin
```
로그인한 뒤 변경해주자

유저, Repository를 생성하고 권한을 줘서 시작할 수 있다.
   
### 7-3. (참고)Repository 삭제 권한 추가
![Image](https://github.com/user-attachments/assets/8ecd44e4-c861-4259-b460-1648cb56ef36)

처음에는 Repository의 삭제권한이 없다 해당 삭제권한을 만들기 위해서는 config파일을 수정해야함

![Image](https://github.com/user-attachments/assets/ded4232c-f0df-479c-b2d2-2bb6d995762f)

공식문서로는 config.ini 파일에 RepositoryDeleteEnabled를 변경하면 됨

문제는 config파일이 어디있는지인데
```
/volume1/@docker/btrfs/subvolumes/"해쉬번호"/opt/svnadmin/data/config.ini
```
해당 경로에 설치된다.


[찾은 방법 참고]
```
//root로 유저변경
# sudo -i

//config.ini 파일을 전체경로에서 찾기
# find / -type f|grep config.ini
```

### 7-4. SVN 다운로드 경로
```
https://{NAS-IP 혹은 도메인}/svn/repo_name
https://svn.example.synology.me/example
```


### \[참고링크]
* SVN : [https://enjoy-dev.tistory.com/16](https://enjoy-dev.tistory.com/16)
* SVN config : [https://svnadmin.insanefactory.com/documentation/](https://svnadmin.insanefactory.com/documentation/)

