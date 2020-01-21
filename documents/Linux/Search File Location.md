# Search File Location

> linux의 파일 위치 검색에 대하여 설명한다.

<br>

리눅스에서 특정 파일의 위치를 검색하는 명령어는 여러가지가 있지만, 가장 많이 사용되는 명령어는 `find` 이다. 사용법은 다음과 같다.

<br>

### find 경로 옵션 조건 action

```shell
[옵션]
-> -name, -user(소유자), -newer(전, 후), -perm(허가권), -size(크기)

[action]
-> -print(기본 값), -exec(외부 명령 실행)

[기본 사용 예]
$ find /etc -name "*.conf"	-> '/etc' 디렉터리 하위에 확장명이 '.conf'인 파일 검색
$ find /home -user ubuntu	-> '/home' 디렉터리 하위에 소유자가 'ubuntu'인 파일 검색
$ find ~ -perm 644	-> 현재 사용자의 홈 디렉터리 하위에 허가권이 644인 파일 검색
$ find /usr/bin -size +10k -size -100k
	-> /usr/bin 디렉터리 하위에 파일 크기가 10KB~100KB인 파일 검색
	
[고급 사용 예]
$ find ~ -size 0k -exec ls -l { } \;
	-> 현재 사용자의 홈 디렉터리 하위에 파일 크기가 0인 파일의 목록을 상세히 출력
$ find /home -name "*.swp" -exec rm { } \;
	-> /home 홈 디렉터리 하위에 확장명이 *.swp인 파일을 삭제
```

