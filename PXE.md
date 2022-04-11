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

### <br> 1. DHCP 설치/설정/실행법 </br>

```
yum -y install dhcp
