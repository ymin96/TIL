# Linux cheat sheet

> linux 에서 자주 사용하는 명령어를 정리한다.

<br>

### ls

list 의 약자로 Windows 의 dir 명령과 같은 역할이다. 해당 디렉터리(폴더) 에 있는 파일의 목록을 나열해준다.

```shell
$ ls					-> 현재 디렉터리의 파일 목록
$ ls /etc/systemd		-> /etc/systemd 디렉터리의 목록
$ ls -a					-> 현재 디렉터리의 목록(숨김 파일 포함)
$ ls -l					-> 현재 디렉터리의 자세한 목록
$ ls *.conf				-> 확장자가 conf인 목록을 보여줌
$ ls -l /etc/systemd/b*	-> /etc/systemd 디렉터리에 있는 목록 중 앞 글자가 'b'인 것의 목록을 							   자세하게 보여줌
```

<br>

<br>

### cd

Change Directory 의 약자로 디렉터리를 이동하는 명령이다.

```shell
$ cd		-> 현재 사용자의 홈 디렉터리로 이동. 만약 현재 사용자가 root면 /root 디렉터리로 이동
$ cd ~ubuntu		-> ubuntu 사용자의 홈 디렉터리로 이동
$ cd ..				-> 바로 상위의 디렉터리로 이동
$ cd /etc/systemd	-> /etc/systemd 디렉터리로 이동
$ cd ../etc/systemd	-> 현재 디렉터리의 상위로 이동 후, 다시 /etc/systemd 로 이동	
```

<br>

<br>

### pwd

Print Working Directory 의 약자로 현재 디렉터리의 전체 경로를 화면에 보여준다.

```shell
$ pwd		-> 현재 작업 중인 디렉터리의 경로 출력
```

<br>

<br>

### rm

Remove 의 약자로 파일이나 디렉터리를 삭제한다. 삭제할 때는 현재 사용자가 파일이나 디렉터리를 삭제할 권한이 있어야 한다. 단 **root** 사용자는 모든 권한이 있으므로 제약이 없다.

```shell
$ rm abc.txt		-> 해당 파일을 삭제(내부적으로 'rm -f' 로 연결된다)
$ rm -i abc.txt		-> 삭제 시 정말 삭제할 지 확인하는 메시지가 나옴
$ rm -f abc.txt		-> 삭제 시 확인하지 않고 삭제함(f은 force의 약자)
$ rm -r abc			-> abc 디렉터리와 그 아래에 있는 하위 디렉터리를 강제로 전부 삭제한다.
```

<br>

<br>

### cp

Copy의 약자로 파일이나 디렉터리를 복사한다. 새로 복사한 파일은 복사한 사용자의 소유가 된다. 그러므르 명령을 실행하는 사용자는 해당 파일의 읽기 권한이 필요하다.

```shell
$ cp abc.txt cba.txt		-> abc.txt 를 cba.txt 라는 이름으로 바꿔서 복사
$ cp -r abc cba				-> 디렉터리를 복사. abc 디렉터리를 cba 디렉터리로 복사
```

<br>

<br>

### touch

크기가 0인 새 파일을 생성하거나, 이미 파일이 존재한다면 파일의 최종 수정 시간을 변경한다.

```shell
$ touch abc.txt		-> 파일이 없을 경우엔 abc.txt라는 빈 파일을 생성하고, abc.txt가 있을 경우엔
					   파일의 최종 수정 시간을 현재 시간으로 변경
```

<br>

<br>

### mv

Move 의 약자로 파일이나 디렉터리의 이름을 변경하거나, 다른 디렉터리로 옮길 때 사용한다.

```shell
$ mv abc.txt /etc/systemd/		-> abc.txt를 /etc/systemd/ 디렉터리로 이동
$ mv aaa bbb ccc ddd			-> aaa,bbb,ccc 파일을 '/ddd' 디렉터리로 이동
$ mv abc.txt www.txt			-> abc.txt의 이름을 www.txt로 변경해서 이동
```

<br>

<br>

### mkdir

Make Directory 의 약자로 새로운 디렉터리를 생성한다. 생성된 디렉터리는 명령을 실행한 사용자의 소유가 된다.

```shell
$ mkdir abc		-> 현재 디렉터리 아래에 '/abc' 라는 디렉터리 생성
$ mkdir -p /def/fgh		-> /def/fgh 디렉터리를 생성하는데, 만약 '/fhg'의 부모 디렉터리인 '/def'
						   디렉터리가 없다면 자동 생성(p는 Parent의 약자)
```

