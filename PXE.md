# PXE, Kickstart

<br>

## 들어가기 전 용어 설명

<br>

- <b>PXE </b>   
Pre-boot eXecution Environmet의 약자이다.   
PXE는 클라이언트/서버 인터페이스의 산업계 표준으로서, 원격지에 있는 관리자가, 아직 운영체계조차 적재되지 않은 채 네트워크에 접속된 컴퓨터를 구성하고, 부팅할 수 있도록 해준다.
PXE를 사용하면 클라이언트 PC에 운영체계, 심지어 하드디스크조차 반드시 있어야 할 필요가 없다.
클라이언트 PC는 하드웨어나 소프트웨어 고장시 재부팅 될 수 있다. 이것은 관리자가 문제점을 진단하고, 어쩌면 해결할 수도 있게 해준다. PXE는 공급자에 독립적이기 때문에, 새로운 형태의 컴퓨터도 손쉽게 네트워크에 추가할 수 있다.   

> 즉, OS 설치 시 부팅 CD를 삽입하고 부팅하던 과정을 네트워크에서 지원하도록 해준다.

- <b>KICKSTART</b>   
리눅스 설치시 사용자는 지역이나 시간, 패스워드, 언어지정, 마우스 설정 등을 일일히 해야한다. 하지만 KICKSTART는 이러한 설정들을 미리 파일에 지정하여 리눅스 설치 프로그램이 설정 파일을 보고 자동으로 설치를 하는 방법이다.

- <b>TFTP</b>   
이더넷을 이용하여 파일을 다운받는 프로토콜이다. UDP 방식을 사용하고 매우 간단한 프로토콜로 구성되므로 부트로더와 같은 작은 크기의 프로그램에서 수행할 수가 있다. 보통 BOOTP와 연동하여 네트워크를 부트를 할 때 사용하게 된다.   

> 즉, 부트로더를 제공하고 클라이언트가 이를 다운받기 위해 사용된다.

- <b>DHCP </b>   
조직 내의 네트워크 상에서 IP 주소를 중앙에서 관리하고 할당해줄 수 있도록 해주는 프로토콜이다.   
DHCP는 주어진 IP 주소가 일정한 시간동안만 그 컴퓨터에 유효하도록 하는 "임대" 개념을 사용한다.   
DHCP는 영구적인 IP 주소를 필요로 하는 웹서버에 대해서는 정적인 주소를 제공한다.

> 즉, IP를 수동으로 설정하지 않고 자동으로 설정되기 위해서는 DHCP 서버가 필요하다.

- <b>vmlinuz 파일 </b>   
vmlinuz는 리눅스 커널을 컴퍼일한 결과인 바이너리 파일을 bzimage를 사용하여 gzip으로 압축한 것이다. 리눅스 부팅시 이 파일 압축을 해제되며 메모리에 로딩되어 리눅스의 운영이 시작된다.

- <b>initrd.img </b>   
커널 모듈을 모아놓은 이미지로 커널이 로딩되기 전에 메모리에 미리 로딩된다. 이 모듈을 예를 들어 마우스 드라이버나 그래픽 카드 모듈 등이 있다. 인텔 크래픽 카드를 사용하는 본체에 리눅스를 부팅하게 되면 initrd.img의 인텔 그래픽 카드 모듈이 메모리에 로딩된다.

- <b>SYSLINUX </b>   
MS-DOS/Windows FAT 파일 시스템에서 실행 리눅스 운영 체제용 부트로더이다. 리눅스 처음 설치를 단순화하기 위한, 구조 및 기타 특수 목적의 부팅 디스크 생성에 있다.   
제대로 구성되어 있으면, SYSLINUX는 완전히 원시 부트 플로피 이미지 배포에 대한 필요성을 제거하는 데 사용할 수 있다. 

- <b>pxelinux.0 </b>   
SYSLINUX 패키지에 포함되어 있는 파일로 대부분의 시스템에는 기본적으로 설치가 되어있다.   
pxelinux.0는 네트워크 부트로더로 리눅스의 grub이나 lilo와 같은 역할을 한다.

<br>

## PXE의 동작원리

<br>

<img src=./image/pxe.img>   

기본 동작은 위와 같은 구성으로 되어 있다. PXE는 기본적으로 클라이언트이기 때문에 서버가 필요하고, 서버는 DHCP 서버와 tftp 서버, 초기 램디스크 이미지(initrd.img)와 커널 이미지(vmlinuz)가 있으면 된다.

<br>

## 동작 순서

<br>

1. DHCP 서버를 찾는다.
2. DHCP 서버에서 주소를 할당받는다.
3. DHCP 서버에서 지정된 pxeclient 들에 대한 부팅파일(pxelinux.0) 정보를 얻는다.
4. TFTP 서버를 통해 pxelinux.0 와 menu.c32 파일을 받는다.
5. 네트워크 부팅이 이루어지며 /[TFTP 관리디렉토리]/pxelinux.cfg/default 파일에 지정된 메뉴들이 나타난다.
6. 메뉴들중 하나를 선택하면 설정되어있는 vmlinuz 커널을 메모리에 로딩하며 초기 설치 화면이 나타난다(initrd.img 은 하드웨어 설정시 필요 모듈을 로드할때 쓰임)

6번이 진행되될때 pxelinux.cfg/default 파일에 kickstart 파일이 지정되어 있다면 바로 자동 설치가 될 것이고
없다면 설정을 하나하나 잡아가야한다.

리눅스 설치파일은 자신이 nfs, ftp 서버를 사용하여도 되지만 이미 공개되어 있는 저장소(예를 들어 ftp.daum.net) 같은 곳을 지정해서 사용해도 된다. 학교나 회사등 외부 네트워크로 접속하기 힘든 경우는 자신이 nfs 나 ftp 서버를 구축하고 설치파일을 자신의 서버에서 배포해야한다.


<br>

## 실습

<br>

### <b> 1. 패키지 설치</b>

***
```
yum -y install dhcp 
yum -y install syslinux
yum -y install tftp-server
yum -y install xinetd
yum -y install vsftpd

# syslinux는 부팅 메뉴 구성을 위한 파일과 부트로더 파일을 제공해준다.
# tftp를 통해 커널 이미지 파일과 syslinux가 제공해주는 부팅 관련 파일을 전송해준다.
# tftp는 xinet 데몬에 의해서 동작하는 데몬이기 때문에 설치.
# vsftp를 통해 운영체제 설치 시 필요한 파일을 전송한다.
```
TFTP 서버 설정.
***
`vi /etc/xinetd.d/tftp`에서 14번 라인 disable = no로 변경해주기.

<br>

### <b> 2. CD 마운트</b>
***
```
mount -t iso9660 /dev/cdrom /mnt

# 리눅스 ISO 파일을 /mnt에 마운트 해준다. (iso9660 형식으로 마운트, /mnt = CD-ROM 마운트 포인트)
# 여기서 iso9660 파일 시스템 타입으로 마운트 해주는 이유는 CD-ROM의 공식적인 파일시스템 타입이 iso9660이기 때문이다. 
# CD-ROM에 마운트시에는 반드시 iso9660 타입을 사용을 해줘야한다.
```
### <b> 3. CD 이미지 복사</b>
***
```
mkdir -p /var/ftp/myCentos
cp -r /mny/* /var/ftp/myCentos &

# CD 이미지 파일을 복사하기 위해 myCentos 디렉터리 생성 후 싹 다 복사
# 이 작업은 로컬저장소를 만드는 과정으로 네트워크를 통해 부팅을 한 시스템(PXE)는 운영체제 설치에 필요한 파일을 vsftp를 통해 다운받는다.
```

### <b> 4. SYSLINUX 패키지 복사</b>
***
```
cd /usr/share/syslinux
cp menu.c32 /var/lib/tftpboot
cp pxelinux.0 /var/lib/tftpboot
```

### <b> 5. 커널 이미지 파일 복사</b>
***
```
mkdir -p /var/lib/tftpboot/centos
cp /var/ftp/myCentos/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/centos/
```

### <b> 6. 부팅 메뉴 구성하기</b>
***
```
mkdir /var/lib/tftpboot/pxelinux.cfg
vi /var/libe/tftpboot/pxelinux.cfg/default

default menu.c32
prompt 0
timeout 300
ontimeout local

LABEL local
        MENU LABEL ^0) Boot Local hard Disk
        localboot 0
LABEL CentOS 7
        MENU LABEL ^1) CentOS 7 Install (VSFTP)
        KERNEL /centos/vmlinuz
        APPEND initrd=/centos/initrd.img repo=ftp://192.168.10.100/myCentos
LABEL CentOS 7
        MENU LABEL ^2) CentOS 7 Install (Mirror)
        KERNEL /centos/vmlinuz
        APPEND initrd=/centos/initrd.img repo=http://mirror.kakao.com/centos/7.9.2009/isos/x86_64/

# 0번 메뉴 -> localboot 0 하드디스크부팅
# 1번 메뉴 -> 커널이미지 파일(vmlinux,initrd.img)을 실행후 설치에 필요한 데이터를 FTP서버에서 다운로드합니다.
# 2번 메뉴 -> 커널 이미지 파일(vmlinux,initrd.img)을 실행후 설치에 필요한 데이터를 Kakao Mirror 서버에서 다운로드합니다.
# ftp://xxx.xxx.xxx.xxx/centos7 부분에는 현재 자신의 IP를 입력합니다. [ifconfig 명령어로 확인가능]
```

### <b> 7. DHCP 서버 구성하기 </b>
***
```
vi /etc/dhcp/dhcpd.conf

subnet 192.168.10.0 netmask 255.255.255.0 { #아이피와 서브넷 지정 옵션
        range 192.168.10.120 192.168.10.200; #시작 IP와 끝 IP 설정
        option routers 192.168.10.2; #게이트웨이 설정 옵션
        option broadcast-address 192.168.10.255; #브로드캐스트 설정
        option subnet-mask 255.255.255.0; #넷마스크 설정 옵션
        option domain-name-servers 8.8.8.8; #네임서버 설정
        default-lease-time 3000; #임대시간(s)
        max-lease-time 6000; #최대 임대시간(s)
        filename "pxelinux.0"; #부팅 파일 이름(syslinux)
}
```
### <b> 8. 서비스 실행 </b>
***
```
systemctl start vsftpd xinetd tftp dhcpd
systemctl stop firewalld
systemctl disable firewalld
```

### <b> 9.동작 확인 </b>
***
OS를 넣지 않은 PXE_Clinet를 실행. 이때 네트워크는 bridge 형식으로 진행해준다.   
OS를 넣지 않음에도 실행되는 모습을 확인할 수 있음.   