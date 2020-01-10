# Crontab 사용법

> 리눅스용 작업 스케줄러인 Crontab의 사용법을 설명한다. 



### Crontab 기본 명령어



#### 작업 등록

```shell
$ crontab -e
```

vi 편집기나 nano 에디터로 사용자에 대한 cron 작업을 직접 등록/수정 할 수 있다.



#### 작업 확인

```shell
$ crontab -l
```

현재 크론탭에 등록되어 있는 작업들을 확인 할 수 있다. 



#### 작업 삭제

```shell
$ crontab -r
```

현재 크론탭에 있는 모든 작업을 삭제한다. 



---

### 크론 작업 등록



#### 작업 등록 형식

``` shell
*			*			*			*			*		 <실행할 명령어>
분(0~59)    시간(0~23)   일(1~31)    월(1~12)    요일(0~7)
```

순서대로 **분-시간-일-월-요일** 순이고 요일에서 0,7이 일요일 (1~6) 이 (월~토) 를 나타낸다.

 

#### 예시

```shell
* * * * * /usr/every_1min.sh
```

매 1분 마다 /usr/every_1min.sh 를 실행

```shell
15,45 * * * * /usr/every_30min.sh
```

매 시 15분, 45분 마다 /usr/every_30min.sh 를 실행

```shell
*/10 * * * * /usr/every_10min.sh
```

10분마다 /usr/every_10min.sh 를 실행

```shell
0 2 * * * /usr/backup.sh
```

매일 2:00 에 /usr/backup.sh 를 실행

```shell
30 */6 * * * /usr/every_6hours.sh
```

매 6시간마다 수행(00:30, 06:30, 12:30, 18:30)

```
30 1-23/6 * * * /usr/every_6hours.sh
```

1시부터 매 6시간마다 수행(01:30, 07:30, 13:30, 19:30)

```
0 8 * * 1-5 /usr/weekday.sh
```

평일(월 ~ 토) 8:00에 실행

```
0 8 * * 0,6 /usr/weekend.sh
```

주말(토,일) 8:00에 실행



---

### 크론 로깅(cron logging)



프로그램이 잘 작동하는지 로그를 남기고 싶다면 다음과 같은 방법을 이용하면 된다.

```
* * * * * /usr/dev/test.sh > /usr/dev/test.sh.log 2>&1
```

위 코드는 test.sh 의 출력으로 test.sh.log 파일을 갱신해준다. 만약 로그 기록을 지속적으로 관리해야 한다면 

```
* * * * * /usr/dev/test.sh >> /usr/dev/test.sh.log 2>&1
```

와 같이 쓰면 된다. 위 코드는 test.sh.log 파일의 끝에 이어서 기록하게 된다. 단 로그가 너무 쌓이면 성능저하가 올 수 있으니 주기적으로 로그 삭제를 해주는 작업이 필요하다.

로그가 필요 없는 크론을 원한다면 아래와 같은 코드를 쓰면 된다.

```
* * * * * /usr/dev/test.sh > /dev/null 2>&1
```



---

### 크론탭 백업(crontab backup)



실수로 `crontab -r` 명령어를 쓰거나 crontab 디렉토리를 삭제하여 기록이 날아갔을 때를 대비하여 백업을 하고 싶다면 다음 명령어를 쓰면 된다.

```
crontab -l > /home/bak/crontab_bak.txt
```

위의 명령어도 크론탭을 이용하여 아래와 같이 자동화가 가능하다.

```
0 1 * * * crontab -l > /home/bak/crontab_bak.txt
```

이렇게 하면 매일 1:00 가 되면 지정된 위치에 크론탭 내용을 덮어쓰기 형태로 저장해준다.



---

### Devian/Ubuntu 에서 cron 작동시키기



#### Start cron service

```shell
# sudo /etc/init.d/cron start
or
# sudo service cron start
```



#### Stop cron service

```shell
# sudo /etc/init.d/cron stop
or
# sudo service cron stop
```



#### Restart cron service

```shell
# sudo /etc/init.d/cron restart
or
# sudo service cron restart
```

