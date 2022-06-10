# ufw 관련
```bash
# ufw 상태 확인
$ sudo ufw status numbered

# sshd: SSH 연결을 받아주기 위해(inbound) 대기하는 프로세스 라고 이해하면 된다.
$ sudo service sshd status

# debian인지 
$ uname -a
```

# 유저 및 그룹 관련

```bash
#youngmki가 그룹에 잘 들어있는지
groups youngmki

# 유저를 생성하고
$ sudo adduser new_username

# 비밀번호 순서대로 확인
# 단 difok의 경우, root에서는 동작하지 않는다. 보여주기 위해서 유저 하나 더 만들어서, 루트에서 chage -m 0 [new-user], 유사한 비밀번호로 변경 시도 해보면 된다.

# 어떻게 바뀌었는지
$ sudo vi /etc/pam.d/common-password 

# 만료 정책 (/Pass)
$ sudo vi /etc/login.defs

# 유저 생성확인
$ cat /etc/passwd | grep '[user]'

# 그룹 삭제 (groupdel)
$ sudo groupdel evaluating

# 그룹 추가
$ sudo groupadd evaluating

# 그룹 생성되었는지 확인
$ getent group

# 유저를 그룹에 추가
$ sudo usermod -aG evaluating new_username

# 해당 유저가 속한 그룹 표시
$ groups [user]

# 비밀번호 관련 규칙 확인 
chage -l new_username
```

- 비밀번호 설정 관련 파일은 /etc/pam.d/common-password 
- 패키지 설치 필요, libpam-pwquality

- 만료날짜 설정: /etc/login.defs
현재 이미 만들어진 것에 대해서는 적용이 되지 않으므로, chage -[flag] 명령어를 통해 추가 수정

해당 방식으로 변경하는 것에 대한 장점: 비밀번호 규칙을 생성하는 것이 비교적 간단하다고 느꼈다. 크래딧을 받으면서 일정 크래딧을 넘으면 비밀번호가 허가된다. 흥미로운 포인트였다. 간단하지만 동시에 최댓값 최솟값 같이 세부적인 조정도 모두 가능했다.

단점? 이라고 하기엔 애매하지만 간과하기 쉬운 부분이 기존의 비밀번호는 적용되지 않고, 앞으로 생성될 유저의 비밀번호에만 적용이 된다는 점.

# Hostname and partitions

```bash
# 호스트 이름 확인
$ hostnamectl

# 호스트 이름 변경
$ hostnamectl set-hostname [new_hostname]

# 호스트 파일 변경
$ sudo vim /etc/hosts
들어가서 수정 후

# 리부팅
$ sudo reboot

# 파티션
$ lsblk

## 아래 무시
## 파티션
#$ fdisk -l

## 파티션 별 사용량
#$ df -h
```

# Sudoers 관련
```bash
# sudo 설치 유무 확인
$ sudo --version

# sudo 그룹에 들어있는지 확인
$ getent group | grep "sudo"

# 아까 생성한 유저를 "sudo" group에 부여
$ su -
$ usermod -aG sudo [user]

# sudo vs su 설명 후 sudo 명령어 시연
$ sudo su -		 # root 권한의 부여 (비로그인쉘 .bash_profile)
$ sudo -i 		 # 로그인 쉘 (.bashrc)

$ sudo -s		 # 위의 명령어와 차이가 있다면, 위는 홈 디렉토리가 /root로, -s는 현재 기존 경로 유지, su - 는 계정을 독립적으로 root로 전환, 아래는 환경 변수들을 root 쪽으로 모두 넘겨줌
$ sudo [command] # 해당 명령어만 

# visudo
$ visudo
# sudo 편집
$ sudo vi /etc/sudoers
```

극단적인 예시로 Ubuntu, MAC OS와 같은 특정 운영체재에서는 root 유저 계정은 보안적인 이유로 비활성화되어 있다. 즉 root에 어떠한 비밀번호도 없고, su를 사용하여 root로 유저를 이동시킬 수도 없다.

이때 사용하는 명령어가 `sudo`이다. `sudo`와 함께 `su -` 명령어를 사용하고 현재 로그인한 사용자 암호를 입력한다.

`sudo` 명령어는 다른 유저로써 프로그램을 실행하는 것을 허용한다.

만약 유저에 `sudo` 가 부여되면, su 명령이 root로 호춛된다. 이때, `sudo su -`는 `su`를 실행하는 것과 동일한 효과가 있다.

sudo에서 로그인 세션으로 진입하기 위해선 `-i` 옵션을 통해, `su -`와 같은 동작을 할 수 있다.
```bash
$ sudo -i
``` 

su 대비 sudo의 장점은 루트 암호를 여러 관리 사용자 계정 간에 공유할 필요가 없다는 것입니다. (root는 관리자를 넘어 슈퍼 유저, 그 외에도 관리자 계정이 있을 수 있다.)

visudo vs /etc/sudoers
- sudoers가 망가지면 sudo 명령어 자체가 실행되지 않음. 그래서 visudo를 경유해서 편집할 수 있다면 그렇게 하는 것이 좋다.

## sudoers 문법 예시 

- %admin 192.168.1.0/255.255.255.0=(apache, myadmin) /usr/bin/sort, /usr/bin/zip 
> admin 그룹에 속한 계정은, 192.168.1.0/255.255.255.0 대역에서 접속할 때, apache 와 myadmin 계정의 권한을 빌려, /usr/bin/sort와 /usr/bin/zip 명령어를 사용할 수 있음을 명시 

- abc ALL=(ALL) ALL
> abc 계정은 어디서나 접근 가능하며, 누구의 권한도 빌릴 수 있고, 모든 명령어를 사용할 수 있다. 
> 
# ufw

```bash
# ufw 설치 확인
$ sudo ufw --version

# ufw 상태 확인
$ sudo ufw status numbered

# ufw enable / disable
$ sudo ufw enable 
$ sudo ufw disable

# 8080 포트 추가
$ sudo ufw allow 8080 

# 삭제
$ sudo ufw status numbered
$ sudo ufw delete [숫자] 
```

ufw 의 장점 (Uncomplicated Firewallu)
- 기존의 net filtering(port / port / protocol 방화벽)은 iptable 명령어를 통해 이뤄졌는데 이 명령어에 익숙해지기 위해서는 상당히 많은 노력을 요구했다. 이를 좀 더 간단하게 사용할 수 있는 프론트엔드인, ufw를 통해 net filtering(방화벽) 구성을 할 수 있으면서 동시에 쉬운 인터페이스를 제공한다.


# ssh

```bash
# ssh 상태 확인
$ sudo service sshd status

# ssh 포트 4242만 있다는 것 확인 ("Port "검색)
$ sudo vim /etc/ssh/sshd_config 

# ssh 연결 (호스트에서)
$ ssh [username]@127.0.0.1 -p 4242

# ssh 연결 종료
$ exit

# 혹시나 재시작이 필요하다면
$ sudo systemctl restart ssh


```

ssh의 가치
- 전달하는 패킷의 암호화 (기존의 rsh, rlogin, telnet ftp 등을 제공)
- TCP 의 threeway-handshake와 긴밀하게 동작한다는 점
- IP spoofing (IP 변조) 방지 기능 제공


# Script monitoring
```bash
#!/bin/bash
# uname: 시스템의 정보 확인 (a: architechture, 모든 정보)
ARC=$(uname -a)
# cpuinfo를 확인 (더 자세한 내용은 설명)
PCPU=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l)
# vCPU -> 하이퍼스레드의 총합 (즉, 물리적 코어("cpu cores") * 물리적 CPU("physical id") * 2)
VCPU=$(grep "^processor" /proc/cpuinfo | wc -l)
# 메모리 관련 명령어 free 현재 차지하고 있는 메모리량 (free -m), 보여주면 설명 가능
MEMTOTAL=$(free -m | awk '$1 == "Mem:" {print $2}')
MEMUSE=$(free -m | awk '$1 == "Mem:" {print $3}')
# USE/TOAL
MEMAVAIL=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')
# df 메모리 관련 명령어 
DISKTOTAL=$(df -Bg | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}')
DISKUSE=$(df -Bm | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}')
DISKAVAIL=$(df -Bm | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} {ft+= $2} END {printf("%d"), ut/ft*100}')

# 순간의 정보를 확인하려면 -b 옵션 추가(batch 모드)
# -n : top 실행 주기 설정(반복 횟수)
# us: user level 에서 사용하고 있는 CPU 비중
# sy: system level에서 사용하고 있는 CPU 비중
CPUL=$(top -bn1 | grep '^%Cpu' | cut -c 9- | xargs | awk '{printf("%.1f%%"), $1 + $3}')
#BTT를 직접 쳐보자
BTT=$(who -b | awk '$1 == "system" {print $3 " " $4}')
#lsblk 명령어를 통해, lvm 확인 가능
LVMC=$(lsblk | grep "lvm" | wc -l)
# 없다면 no, 있다면 yes
LVMU=$(if [ $LVMC -eq 0 ]; then echo no; else echo yes; fi)
#You need to install net tools for the next step [$ sudo apt install net-tools]
TCP=$(cat /proc/net/sockstat{,6} | awk '$1 == "TCP:" {print $3}')
# 유저 수
ULOG=$(users | wc -w)
# hostname
IP=$(hostname -I)
MAC=$(IP link show | awk '$1 == "link/ether" {print $2}')
CMDS=$(journalctl _COMM=sudo | grep COMMAND | wc -l)
wall "	#Architecture: $ARC
	#CPU physical: $PCPU
	#vCPU: $VCPU
	#Memory Usage: $MEMUSE/${MEMTOTAL}MB ($MEMAVAIL%)
	#Disk Usage: $DISKUSE/${DISKTOTAL}Gb ($DISKAVAIL%)
	#CPU load: $CPUL
	#Last boot: $BTT
	#LVM use: $LVMU
	#Connexions TCP: $TCP ESTABLISHED
	#User log: $ULOG
	#Network: IP $IP ($MAC)
	#Sudo: $CMDS cmd"
```
vCPU: 하이퍼 쓰레드가 허용되어 있다면, 하이퍼 쓰레드 수의 총합, 그게 아니라면 1

인텔® 하이퍼 스레딩 기술은 각 코어에서 둘 이상의 스레드를 실행할 수 있는 하드웨어 혁신입니다. 스레드가 많을수록 더 많은 작업을 병렬로 수행할 수 있습니다. ... 이는 하나의 물리적 코어가 다른 소프트웨어 스레드를 처리할 수 있는 두 개의 "논리적 코어"처럼 작동한다는 것을 의미합니다.

# cron

```bash
# cron 상태
$ service cron status
# cron 편집
sudo crontab -u root -e
# crontab 일시 중지
$ sudo /etc/init.d/cron stop
$ service cron stop (리턴해주는 메시지가 없음)
# crontab 시작
$ sudo /etc/init.d/cron start
$ service cron start (리턴해주는 메세지가 없음)
```
