# 스프링부트 WAR 배포하기(gradle)

> 이클립스에서 스프링부트 + gradle 프로젝트의 WAR파일 배포 방법을 설명한다.



### SpringBootServletInitializer 상속

```java

package com.about_time;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class AboutTimeApplication extends SpringBootServletInitializer {

	public static void main(String[] args) {
		SpringApplication.run(AboutTimeApplication.class, args);
	}

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(AboutTimeApplication.class);
	}

	@Bean(name = "uploadPath")
	public String uploadPath() {
	    return "/Pictures/AboutTime";
	}
}

```

가장 먼저 해야할 일은 main 메소드가 있는 클래스에 SpringBootServletInitializer을 상속시키고 configure 함수를 override 하고 위 코드처럼 작성해준다.



### providedRuntime 설정

```groovy
providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
```

외부 톰캣을 사용한다는걸 알리기 위해 build.gradle에 들어가 dependencies 탭 안에 위 코드를 추가해줍니다.



### WAR 생성 관련 설정

```groovy
plugins {
	id 'war'
}

bootWar {
    archiveBaseName = "-"
    archiveFileName = "-.war"
    archiveVersion = "0.0.0"
}
```

build.gradle에 들어가 plugins를 설정해주고 bootWar로 파일 이름 버전 등을 설정할 수 있다. 설정이 따로 없다면 프로젝트명 + Version이 붙어서 생성된다.



### WAR 파일 생성 

<img src="C:\Users\Ymin\Documents\TIL\documents\img\Spring\war1.PNG" alt="war1" style="zoom: 80%;" />



<img src="C:\Users\Ymin\Documents\TIL\documents\img\Spring\war2.PNG" alt="war2" style="zoom: 80%;" />



이클립스의 Gradle Tasks 탭으로 들어가 사진 경로의 bootWar을 더블클릭하면 2번째 사진처럼 Gradle Executions 탭으로 자동으로 넘어가 war파일이 생성된다.

생성된 war파일은 프로젝트 폴더의 build/libs/ 폴더로 들어가면 확인 할 수 있다. 

이렇게 만들어진 war파일을 tomcat의 webapps 폴더에 넣어주면 배포 성공.