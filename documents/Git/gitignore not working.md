# gitignore 적용 안될때 

> 이미 저장소에 push 된 파일을 깃 track에서 제외 시키는 방법을 알아본다.



한참 프로젝트를 진행중 git에 추가시키면 안되는 파일을 이미 추가하고 push 했을 경우 뒤늦게 .gitignore에 제외 시킬 파일을 입력해도 이미 track 된 파일은 제거 되지 않는다. 이럴 때는 다음과 같은 코드를 입력하면 해결 된다.

```shell
$ git rm -r --cached .
$ git add .
$ git commit -m "apply .gitignore"
```



`git rm -r --cached .` 명령어는 Git Staging Area에 있는 모든 파일을 삭제해준다. 즉 물리적 자료는 남기고 그 자료들을 Git에서 추적하지 않게 해준다. 그 뒤에 다시 `git add .` 명령어로 모든 파일을 add 해주면 수정한 .gitignore이 정상적으로 적용된다. 







---

토이 프로젝트 진행중 위의 방법대로 해도 gitignore 가 적용 안되는 문제가 있었는데 한참을 고생하다 이유를 알아보니 gitignore 파일이 프로젝트 하위폴더에 하나 더 있었다;;;

이클립스에서 프로젝트 생성할때 같이 생성된거같은데 바로 내용은 최상단 gitignore에 복붙하고 삭제한뒤 위 방법을 사용하니 정상적으로 적용되었다.