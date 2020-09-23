# DAY83-84

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

   

   ## 파일의 구성요소, 파일의 권한, 계정생성, 계정권한

   ```shell
   # ls -F /
   bin@   dev/  home/  lib64@  mnt/  proc/  run/   srv/  tmp/  var/
   boot/  etc/  lib@   media/  opt/  root/  sbin@  sys/  usr/
   ```

   1. **bin@ [기본 명령어 존재]**   

   2. dev/ [장치 관리자 파일이 존재] 

   3. **home/ [일반 사용자들이 사용하는 홈 디렉토리]** 

   4. lib64@  

   5. **mnt/ [입/출력 장치등 파일 시스템을 임시로 마운트 하는 디렉토리]** 

   6. proc/ [프로세스 정보, 커널 관련 정보 디렉토리] 

   7. run/   

   8. srv/ [FTP 또는 웹 시스템에서 제공하는 데이터를 저장하는 디렉토리]

   9. tmp/ [시스템 사용중에 발생하는 임시데이터가 저장되는 디렉토리]

   10. var/ [시스템 운용체제에 실행되는 데이터 로그 관련정보]

   11. boot/ [부팅에 필요한 커널 파일이 존재하는 곳] 

   12. **etc/ [리눅스 시스템 설정을 위한 각종 파일]**

   13. lib@ [공유 라이브러리]  

   14. media/ [외부장치 연걸할 때 사용되는 곳(USB/CD-ROM)] 

   15. opt/ [리눅스에서 인식하지 못하는 프로그램을 관리] 

   16. root/ [root 계정의 홈디렉토리, /(루트) 와는 다르다] 

   17. lost+found  

   18. sys/ [리눅스 커널과 관련된 디렉토리] 

   19. usr/ [기본 실행파일과 라이브러리 파일, 헤더파일이 존재하는 디렉토리]

       ---------------

   20. lost+found/ [자료를 탐색할 때 버퍼 또는 클립보드를 구현하는 곳]

   21. misc/[기타 파일_ 인식하지 않은 파일의 정보가 있는 곳]

   22. **net/ [네트워크 정보]**

   23. selinux [히든 파일, 분할 시스템 정보 ] 



### 경로

1. 상대경로 : / 가없다
   - "/"이와의 문자로 시작함
   - 현재 디렉토리에서 서브 디렉토리를 이동할 때 서브 디렉토리명 추가로 이동
   - 현재에서 상위로 갈때 ..
   - 상대 경로명은 현재 위치한 디렉토리의 위치에 따라 달라질 수 있음
2. 절대 경로 : /로 시작 
   - 반드시르 루트를 / 로 시작
   - 루트 디렉토리 부터 중간 디렉토리를 모두 표시함
   - 특정 위치를 표시하는 절대 경로명은 항상 동일해야함 



### 디렉토리 파일 생성 규칙 

- / 이외의 문자로 생성
- 알파벳, 숫자, -(빼기),_(언더바), .(마침표) 만 사용가능 
- 사용하면 안됨
  - 공백, *, |, ",',@,#,$,%,^,&
- 대소문자 구별 가능 
  - TEST.java <-구별가능-> Test.java
- 파일과 디렉토리 명을 선언할 때 점(.)으로 시작하면 숨김파일로 간주함



### 상세정보 (ls -al)

```shell
drwxr-xr-x   2 root root    6  9월  9 17:08 hsperfdata_root
```

- d: 파일의 유형[-d | b c P s]
  - [-(일반 정규파일) d(디렉토리) l(심볼) b(블록) c(섹터) P(파이프_ 프로세스간 통신에 사용되는 파일) s(소켓)]
- rwx: 사용권한
- r-x:그룹권한
- r-x: 타인이 접근할 수 있는 권한 



### 

#### find : 파일 찾기 

- find 경로 검색 조건 [옵션] [동작]
- [옵션]
  - name : 파일 이름으로 검색
  - type : 파일 타입으로 검색[b: 블록 | d: 디렉토리 | f:파일 | l : 심볼릭 링크]
  - user : 로그인 아이디로 사용자가 소유한 모든 파일 검색
  - perm : 지정한 접근 권한과 일치하는 파일 검색 
- [동작]
  - exec 명령{} \; : 검색된 파일에 명령을 실행
  - ok 명령{} \; : 사용자의 확인을 받아서 명령을 시행 
  - print: 절대 경로명을 화면에 출력
  - ls : 검색결과가 긴 목록 형식으로 출력



#### cat 

 - ```shell
   cat > 파일명 : 파일에 저장함
   ```

 - ```shell
   cat >> 파일명 : 파일에 추가
   ```

 - ```shell
   cat < 파일 : 표준 입력으로 받음
   ```



> my.txt 파일의 값을 입력 받아 d.txt로 저장

````shell
$ cat < my.txt > d.txt 
````

<img src="../../../Library/Application Support/typora-user-images/image-20200910150123115.png" alt="image-20200910150123115" style="zoom:50%;" /> 



> my.txt의 줄번호 추가해서 다시 res.txt로 저장 

```shell
$ cat -n my.txt > res.txt
```

<img src="../../../Library/Application Support/typora-user-images/image-20200910150445264.png" alt="image-20200910150445264" style="zoom:50%;" /> 



#### grep: 특정 문자열 검색 

- -A 숫자 : 지정 숫자 아래 
- -B 숫자 : 지정 숫자 위 
- -c : 라인포함
- -i : 대소문자 무시 
- -n: 라인번호 출력 



```shell
[hadoop@hadoop test_dir]$ cat > edible
1. apple
2. strawberry
3. watermelon
4. mandarin
5. pear
6. apricot
```



> 귤을 포함한 아래 2개의 과일을 찾아라 

```shell
$ grep -A 2 mandarin edible
```

![image-20200910153653327](../../../Library/Application Support/typora-user-images/image-20200910153653327.png)



> 수박을 포함한 위 1개의 과일을 찾아라

```shell
$ grep -B 1 watermelon edible
```

![image-20200910153807926](../../../Library/Application Support/typora-user-images/image-20200910153807926.png)



> r자가 들어간 과일을 출력하라 

```shell
$ grep -r r edible
```

![image-20200910154202591](../../../Library/Application Support/typora-user-images/image-20200910154202591.png)



> s자가 들어간 과일을 출력하되 라인번호도 출력

```bash
$ grep -r s -n edible
```



> 과일 정렬 

```bash
$ sort -k 2 edible
```



> 과일을 딸기를 기준으로 정렬해라 



> 과일을 숫자를 기준으로 역순 정렬

```bash
$ sort -r edible
```



> edible 파일을 edible2로 하나 생성한 다음 두개의 파일을 병합해서 화면에 출력

```bash
$ sort -m edible edible2 
$ cat edible edible2 
```

- 최상위 루트에서 크기가 1000블록 이상인 파일 검색

```bash
# find / -size +1000 -print
```

- 10일 이전에 수정된 파일 검색 

```bash
$ find .mtime + 10 -print
```



- 5분전에 마지막으로 수정된 파일을 찾을 수 잇다. 

```bash
$ find / -cmin 5 -print
```

- 1일 이내에 수정된 파일을 검색하여 모두 지움 

```bash
$ find . -mtime -1 -exec rm {} \;
```



- /home 디렉토리에 용량이 0인 파일과 디렉토리를 찾음 

```bash
$ find /home -empty -print
```



#### wc : 파일의 크기 정보를 출력 

- wc : Word Count : 특정 파일의 단어 수 
  - c: byte 수
  - w: 단어의 수 
  - l: 라인의 수
  - m: 문자의 수 

> member의 단어 라인 문자수를 출력해보자 

```bash
$ wc member
$ wc -w member
$ wc -m member
$ wc -wcm member
```

