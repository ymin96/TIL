# Linux File Compression

> linux 파일 압축과 묶기에 대하여 알아본다.

<br>

### 파일 압축

리눅스에서 많이 보이는 압축 파일의 확장명은 xz, bz2, gz, zip, Z 등이 있다. 최근에는 xz나 bz2 를 많이 사용하는 추세이다.

#### xz

확장명 xz 로 압축하거나 풀어준다. 비교적 최신의 압축 명령으로 압축률이 뛰어나다.

```shell
$ xz 파일이름		-> '파일이름'을 압축 파일인 '파일이름.xz'로 만든다. 기존 파일은 삭제됨
$ xz -d 파일이름.xz		-> 압축을 풀어줌. 즉 '파일이름.xz'의 압축을 풀어서 '파일이름'으로 만듦
$ xz -l 파일이름.xz		-> '파일이름.xz' 압축 파일에 포함된 파일 목록과 압축률 등을 출력함
$ xz -k 파일이름		-> 압축 후에 기존 파일을 삭제하지 않고 그대로 둠
```

<br>

#### bzip2

확장명 bz2로 압축하거나 풀어준다.

```shell
$ bzip2 파일이름		-> '파일이름'을 압축 파일인 '파일이름.bz2'로 만듦
$ bzip2 -d 파일이름.bz2		-> 압축을 풀어줌. 즉 '파일이름.bz2'의 압축을 풀어서 '파일이름'으로 만듦
$ bzip2 -k 파일이름		-> 압축 후에 기존 파일을 삭제하지 않고 그대로 둠
```

<br>

#### gzip

확장명 gz로 압축하거나 풀어준다.

```shell
$ gzip 파일이름		-> '파일이름'을 압축 파일인 '파일명.gz' 으로 만듦
$ gzip -d 파일이름.gz		-> 압축을 풀어줌. 즉 '파일이름.gz'의 압축을 풀어서 '파일이름'으로 만듦
```

<br>

#### zip/unzip

Windows용과 호환되는 확장명 zip 으로 압축하거나 풀어준다.

```shell
$ zip 새로생성될파일이름.zip 압축할파일이름	-> '압축할파일이름'을 '새로생성될파일이름.zip'으로 만듦
$ unzip 압축파일이름.zip	-> '압축파일이름.zip' 의 압축을 풀어줌 압축 파일은 그대로 남아 있음		
```

<br>

<br>

### 파일 묶기

리눅스(유닉스) 에서는 '파일 압축' 과 '파일 묶기' 는 원칙적으로 별개의 프로그램으로 실행하도록 되어 있다. 물론 사용자 편의성을 위해 한 번에 할 수 있는 옵션도 제공한다. 파일 묶기의 명령어는 `tar`이며 묶어진 파일의 확장명도 `tar`이다.

<br>

#### tar

확장명 tar로 묶음 파일을 만들어 주거나 묶음을 풀어준다.

```shell
[동작]
c(소문자)	-> 새로운 묶음을 만든다.
x	-> 묶인 파일을 푼다.
t	-> 묶음을 풀기 전에 묶인 경로를 보여준다.
C(대문자)	-> 묶음을 풀 때 지정된 디렉터리에 압축을 푼다. 지정하지 않으면 묶을 때와 동일한 디렉터리에  묶음이 풀린다.

[옵션]
f(필수)	-> 묶음 파일 이름 지정. 원레 tar은 테이프 장지 백업이 기본이다(생략하면 테이프로 보내짐)
v	-> visual의 의미로 파일이 묶이거나 풀리는 과정을 보여준다.(생략가능)
J(대문자)	-> tar + xz
z(소문자)	-> tar + gzip
j(소문자)	-> tar + bzip2

[사용 예]
$ tar cvf my.tar /etc/systemd/	-> 묶기
$ tar cvfJ my.tar.xz /etc/systemd/	-> 묶기 + xz 압축
$ tar cvfz my.tar.gz /etc/systemd/	-> 묶기 + gzip 압축
$ tar cvfj my.tar.bz2 /etc/systemd/	-> 묶기 + bzip2 압축
$ tar tvf my.tar	-> 파일확인
$ tar xvf my.tar	-> tar 풀기
$ tar Cxvf newdir my.tar	-> newdir에 tar 풀기
$ tar xfJ my.tar.xz	-> xz 압축 해제 + tar 풀기
$ tar xfz my.tar.gz	-> gzip 압축 해제 + tar 풀기
$ tar xfj my.tar.bz2	-> bzip2 압축 해제 + tar 풀기
```

자주 사용하는 명령어는 `tar xvfF 파일이름.tar.xz` 와 `tar xvfj 파일이름.tar.bz2` 명령 정도가 있다. 