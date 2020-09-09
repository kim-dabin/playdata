# DAY83

## 시스템 확인 

- history : 이제까지 명령을 확인 

  ```shell
      1  systemctl restart network
      2  ip addr
      3  yum -y install openssh*
      4  systemctl restart sshd.service
      5  systemctl enable sshd.service
      6  reboot
      7  vi /etc/hosts
      8  hostname
      9  192.168.56.101 server01
     10  192.168.56.102 server02
     11  192.168.56.103 server03
     12  192.168.56.104 server04
     13  vi /etc/hostname 
     14  systemctl restart network
     15  vi /etc/selinux/config 
     16  systemctl stop firewalld
     17  systemctl disable firewalld
     18  systemctl status firwalld.service
     19  systemctl status firewalld.service
     20  sysctl -w vm.swappiness=100
     21  vi /etc/sysctl.conf
     22  vi /etc/rc.local 
     23  vi /etc/security/limits.conf
     24  reboot
     25  systemctl restart network
     26  vi /etc/hosts
     27  hostnamectl set-hostname server02
     28  hostname
     29  cat /etc/hostname 
     30  systemctl restart network
     31  reboot
     32  vi /etc/hosts
     33  hostnamectl set-hostname server03
     34  hostname
     35  cat /etc/hostname 
     36  systemctl restart network
     37  reboot
     38  vi /etc/hosts
     39  hostnamectl set-hostname server04
     40  hostname
     41  cat /etc/hostname
     42  systemctl restart network
     43  reboot
     44  ls
     45  cd /usr/local/
     46  ls
     47  tar -xvf jdk-8u231-linux-x64.tar 
     48  ls
     49  rm -f jdk-8u231-linux-x64.tar 
     50  ls
     51  ln -s jdk1.8.0_231/ java
     52  ls
     53  vi /etc/profile
     54  source /etc/profile
     55  java -version
     56  exit
     57  ls
     58  cd ..
     59  ls
     60  cd bin/
     61  ls
     62  cd ..
     63  ls
     64  cd proc/
     65  ls
     66  cd ..
     67  ls
     68  cd ~
     69  ls
     70  cd ..
     71  ls
     72  cd usr/
     73  ls
     74  exit
     75  pwd
     76  ls
     77  cd /
     78  ls
     79  cd tmp/
     80  ls
     81  cd ..
     82  ls
     83  cd usr/
     84  ls
     85  cd local/
     86  ls
     87  cd ..
     88  ls
     89  pwd
     90  cd ~
     91  ls
     92  ls -al
     93  pwd
     94  yum install update
     95  yum -y install vim-enhanced
     96  yum install update kernel*
     97  yum install gcc kernel-devel kernel-headers dkms make bzip2
     98  mount -r /dev/cdrom /media/VirtualBoxGuestAdditions
     99  cd /media/VirtualBoxGuestAdditions/
    100  ls
    101  ls -al
    102  reboot
    103  cd /media/
    104  ls
    105  ls -al
    106  mkdir /media/VirtualBoxGuestAdditions
    107  ls
    108  mount -r /dev/cdrom /media/VirtualBoxGuestAdditions
    109  cd /mnt/cdrom/
    110  cd /dev/cdrom 
    111  ls
    112  cd /dev/
    113  ls
    114  mount -r /dev/cdrom /media/VirtualBoxGuestAdditions
    115  cd /media/VirtualBoxGuestAdditions/
    116  출처: https://dewnine.tistory.com/43 []
    117  ./VBoxLinuxAdditions.run
    118  ls
    119  mount -r /dev/cdrom /media/VirtualBoxGuestAdditions/
    120  pwd
    121  exit
    122  mount -r /dev/cdrom /media
    123  ls
    124  cd  /media/
    125  ls
    126  rm -rf VirtualBoxGuestAdditions/
    127  ls
    128  pwd
    129  cd ~/
    130  ls
    131  cd ..
    132  ls
    133  cd home/
    134  ls
    135  cd bigdata/
    136  ls
    137  rm -rf a.txt 
    138  pwd
    139  man date
    140  date
    141  cal 2025
    142  logname
    143  pwd
    144  logname
    145  clear
    146  users
    147  cd /home/
    148  ls
    149  cd ~
    150  useradd hadoop
    151  cd /home/
    152  ls
    153  cd hadoop/
    154  ls
    155  pwd
    156  cd ~
    157  ls
    158  users
    159  who
    160  whoami 
    161  who am i
    162  man uname
    163  uname --all
    164  uname -a
    165  uname -v
    166  hostname
    167  vi /etc/hosts
    168  arch 
    169  env
    170  cd /usr/bin/env
    171  ls
    172  cd /usr/bin/
    173  ls
    174  cat env
    175  env
    176  ls
    177  cd ~
    178  ehco abcd
    179  echo abcd
    180  ls
    181  ehco abdc \n 1234
    182  echo abcd \n 1234
    183  echo -e abcd \n 1234
    184  echo -e 'abcd \n 1234'
    185  echo -e abcd '\n' 1234
    186  which
    187  which echo
    188  which java
    189  java -version
    190  which cp
    191  which mv
    192  history 
    
  ```

  - ![번호] : history의 옛날 명령 다시 실행 

    ```shell
    # !191
    ```

    <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gikgvtla2sj30bk080jt3.jpg" alt="image-20200909171400262" style="zoom:33%;" /> 

  - history -c : 전체 삭제 
  - history -d [번호] : 해당 번호 명령어 기록 삭제 

- i -node : 리눅스에서 파일의 구성정보를 저장

  - (index -Node): 파일의 종류, 소유권, 액세스모드, 파일 갱신일 



## 리눅스 파일의 종류 

### 입출력 파일

1. 일반 파일 : 데이터 관리(단, 이미지 실행파일은 바이너리 타입) 

2. 디렉토리 파일

   - 링크 파일: 원본파일의 별칭을 부여해서 실행하기 좋은 디렉토리에 위치시키는 파일

     1. 하드 링크: 

     - 원본파일을 복사해서 원본파일과 동일한 내용의 다른 사본파일을 생성
     - 원본파일과 사본파일(링크 파일) 삭제는 연관이 없고 수정은 함께 업데이트 됨  

     2. 심볼릭 링크:

     - 바로가기
     - 단축아이콘

3. 특수 파일 

   - 파이프
     - 명령된 파이프 
     - 명령되지 않은 파이프 
   - 소켓
   - 주변장치
     - 버퍼링화 
       - 테이프
       - 디스크
     - 노버퍼링화
       - 터미널

   

