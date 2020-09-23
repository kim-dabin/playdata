# DAY84



## 학습목표

1. 파일의 속성을 확인할 수 있다.
   파일이름 -> I-Node -> DataBlock
2. 파일 권한 접근 설정을 할 수 있다.
3. 기호 접근 권한을 이해할 수 있고 설정할 수 있다.
4. 메모리 관리, 네트워크 설정 등을 할 수 있다.
5. 프로그램 설치를 할 수 있다.
6. 하둡을 설치하고 소프트 링크를 걸 수 있다.
  클러스터를 구현하고 완전 분사모드를 설장할 수 있다.





## 파일 권한

- -rw-rw-r-- 1 hadoop hadoop 45 9월 11 10:40 a.txt
  - [소유자] [그룹] [기타 사용자]
  - r: 읽기, 복사 -> ls로 목록 확인
  - w: 쓰기, 수정, 이동, 삭제 
  - x: 실행파일 -> cd 명령 가능, 이동 또는 복사 가능
- 접근 권한 변경 : chmod -R



### 기호를 이용한 권한 설정

- chmod u+w /etc/sudoers

- chmod [사용자(소유자, 그룹, 기타) | 연산자 기호(권한 더하거나, 뺀다)| 접근 권한 문자(r,w,x)] 파일명

- 사용자(소유자, 그룹, 기타): u(파일소유자), g(그룹), o(기타), a(전체사용자) 

- 연산자 기호 : +, -, = (설정)

- ex)

  - u+w : 파일 소유자 쓰기 권한 부여
  - u-w
  - u=rwx
  - u+x,go+w : 소유자에게 실행 권한 부여와 그룹 및 기타 사용자에게 쓰기 권한 부여 
  - g+w
  - g+wx
  - go+w : 그룹과 기타 사용자에게 쓰기 권한 부여 
  - +wx : 파일을 사용하려는 사용자에게 쓰기, 실행 권한 부여 
  - a+rwx: 모든 사용자에게 읽기, 쓰기, 실행권한 부여
  - o-r

- > a.txt 파일의 권한을 하고 싶은 것으로 바꾸어보자



### 숫자를 이용한 권한 설정

- chmod [사용자(소유자, 그룹, 기타) | 연산자 기호(권한 더하거나, 뺀다)| 접근 권한 문자

- -rw -rw- r--. 1 

- [소유자] [그룹] [기타 사용자]

  - rwx rwx rwx
  - 0~700 0~070 0~007 

- rwx를 2진수(111)로 대체한 다음 8진수(700,070,007)로 환산하여 합산함 

  - 777, 407, 541 .... 

  <img src="../../../Library/Application Support/typora-user-images/image-20200911112405539.png" alt="image-20200911112405539" style="zoom:50%;" /> 

- r-x r-x r-x 555
- rwx r-- r-- 744
- rw- r-- --- 640
- r-- --- --- 400



### 계정정보 확인 

- /etc/passwd
- /home



### 사용자 계정 생성

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gimjf01ge5j30ma0be42x.jpg" alt="image-20200911121248696" style="zoom:50%;" /> 

- 비번지정 후 정보 확인 

  - \# cat /etc/shadow

- 비번 지정 

  - **passwd** **[-k]** **[-l(잠금)]** **[-u(해제)** **[-f]]** **[-d(해제)]** **[-e]** **[-n** **mindays]** **[-x** **maxdays]** **[-w**

    ​    **warndays]** **[-i** **inactivedays]** **[-S]** **[--stdin]** **[username]**

    

> user_01을 생성 

```bash
# useradd user_01
```



> user_01의 비번을 생성 

```bash
# passwd user_01
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gimjlm18epj30s607ywii.jpg" alt="image-20200911121908897" style="zoom:50%;" /> 



> user_02 계정 생성하면서 비번도 함께 생성

```bash
# useradd user_02 -p 1111
```



### 사용자 계정 정보 수정 

- **usermod** [options] LOGIN

> 사용자 user_02에 대한 UID를 변경해보자 

```bash
# usermod -u 2020 user_02
```

> 바뀐 정보 확인 

```bash
# chage -l hadoop
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gimk4nqrtsj313q0agtdr.jpg" alt="image-20200911123727210" style="zoom:50%;" /> 



### 사용자 계정 삭제 

- **userdel** [options] LOGIN
  - options:
    - -r : 홈디렉토리 삭제 
    - -f : 사용중인 계정 이름 포함 강제 삭제 
    - -rf : 가장 강력!

### 시스템 정보 확인

#### mount

- 하드 디스크를 분할을 하게 디ㅗ면 각 디스크 분할의 해당 파티션을 인식할 수 있게 해주는 과정

#### 디스크 관련 명령 

- df(하드 디스크의 남은 용량)
- B - byte df -B 1024, df : 용량을 지정된 값으로 수정(kb 블럭)
- T - 파일 시스템의 종류를 리턴 
- t - 파일 시스템
- h -> 용량을 GB, MB 단위로 출력 



#### 사용 용량 확인

- du
- a: 지정된 디렉토리와 하위 디렉토리
- m: MB단위로 씀 



> 현재 /sbin 디렉토리의 크기를 mb 단위로 출력하자 

```bash
# du -m /sbin
```



> 부팅 정보를 출력(dmesg)



### 부팅 정보, 시스템의 정보를 확인하자 

```bash
# cat /var/log/wtmp
```



- 시스템의 로그인 재부팅 로그아웃 정보를 출력해보자

```bash
# last
```



- 시스템이 부팅된 시점부터 현재까지 모든 사용자의 정보 시스템 재부팅 정보 중 최근 3개의 정보를 호스트 이름을 제외하여 출력한다 

```bash
# last -3 -R
```

![image-20200911154859033](https://tva1.sinaimg.cn/large/007S8ZIlgy1gimpnyoqzlj30t205o0vl.jpg)



- 시스템이 부팅된 시점부터 현재출력한다 까지 모든 사용자의 정보와 시스템 재부팅 정보중 최근 3개의 정보를 출력하되, 호스트 이름을 제일 끝에 





#### lastlog

- 사용자의 마지막 로그인 정보를 출력하는 키워드(시스템 관리자)

```bash
# lastlog -u hadoop
```



#### free 

- 메모리 정보 보기 

- b,k,m,s

- 현재 메모리 사용정보를 MB단위로 출력 

  ```bash
  # free -m
  ```

- 현재 메모리 사용정보를 MB단위로 2초에 한번씩 출력 

  ```bash
  # free -m -s 2
  ```



#### at(작업 예약)/atq(작업 예약 정보 리턴)/ atrm(작업 삭제)

- at: 옵션, 시간, 날짜, 증가시간 

  - q,m(메일 알림),t,f,d,l

- at 명령을 이용해서 2014년 6월 10일 오후 2시 44분에 a3이라는 파일을 생성하고 mytest라는 폴더를 생성하자 (나갈때는 컨트롤 D)

  ```bash
  # systemctl start atd
  # at 16:07 2020-09-11
  at> touch /home/hadoop/test_dir/a3
  at> mkdir /home/hadoop/test_dir/mytest
  at> <EOT>
  job 3 at Fri Sep 11 16:07:00 2020
  Can't open /var/run/atd.pid to signal atd. No atd running?
  ```



## 네트워크 명령

- ifconfig : ip 정보 
- \# cat /etc/sysconfig/network
- \# cat /etc/sysconfig/network-scripts/ifcfg-enp0s8



