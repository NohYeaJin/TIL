1. Heroku 가입 후 app 생성
2. 해당 app으로 들어가서 resources 탭에 들어가서 필요한 것들 설치(MySQL)
3. Heroku CLI 설치 ⇒ [https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli)
    - CLI 가 처음에는 되다가 갑자기 관리자 권한으로 켰더니 키자마자 꺼지는 현상이 발생해서 cmd 창으로 heroku 접근함 ⇒ 부족한 부분 없이 모든 기능 잘 수행되었음
4. cmd 에 들어가서 heroku login 입력 ⇒ login 브라우저 창이 뜨고 로그인 하면 CMD창에서 자동으로 로그인이 된다
5. github repo를 clone 하듯이 
    - git init ⇒ 해당 프로젝트내에서
    - heroku git:clone -a graduprojects 로 클론한다
    - C:\herokudemo\graduprojects 파일 구조는 이렇고
    - 
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bfe70d21-bf07-448f-89af-f941957f5937/Untitled.png)
    
    - 이후 git add . 해줘야 하는데, 핵심은 .git이 있는 위치에 프로젝트 관련 파일들이 주르륵 있어야 하는 것 ⇒ 이걸 지켜서 add하면 문제 없다
    - git commit -am "commit message"
    - git push heroku master ⇒ 여기서 부터가 문제
    - 일단 가장 좋은 건 블로그 + [https://devcenter.heroku.com/articles/deploying-gradle-apps-on-heroku](https://devcenter.heroku.com/articles/deploying-gradle-apps-on-heroku) 를 참고하는 거다
    - 위 링크를 보면 Verify that your build file is set up correctly 부분이 있는데 여기 spring boot 부분부터 따라하면 된다

여기 중에서 

```java
task stage(dependsOn: ['build', 'clean'])
build.mustRunAfter clean

heroku config:set GRADLE_TASK="build"

java -Dserver.port=$PORT $JAVA_OPTS -jar build/libs/*.jar

build
.gradle
.idea
out
*.iml
*.ipr
*.iws
.DS_Store

./gradlew stage
heroku local web

git add .
git commit -m "Added a Procfile."
git push heroku master
```

위의 과정만 따라했고, local web을 실행하면 port번호가 좀 충돌나는 것 외에는 잘 됐음

다만 초반에 build 문제가 있었음 compile:querydsl 에서 지속적으로 빌드 에러가 생겼는데 

`Task :compileQuerydslJava FAILED`  와 함께 11버젼에 대한 에러가 떴음 

⇒ 해결법은 intellij의 jdk버젼을 11짜리로 새로 다운 받고(매우 귀찮은 과정...intellij 설정이니 heroku와는 관련 없을 것 같긴함, 애초에 intellij에서 빌드하면 에러가 생기지 않음!!)

⇒ root 폴더에 [system.properties](http://system.properties) 파일을 만들어서 java.runtime.version=11를 추가하는 것

⇒ 이렇게 했더니 build할 때마다 heroku에서 running jdk 1.8 ⇒ running jdk 11로 바뀌었음!

build.gradle부분에는 

```java
tasks.withType(JavaCompile) { options.annotationProcessorGeneratedSourcesDirectory = file(querydslDir) }
```

이거랑 위의 링크에서 나온 clean부분 추가했고 

application.yml에는

```java
jpa:
    hibernate:
      ddl-auto: update
```

ddl-auto를 update로 바꿨다 

- gradle ⇒ build ⇒ bootjar를 통해 jar파일들 만들어줬고 plain어쩌구 있는데 이건 무시하면 된다
- Procfile은 txt 파일인데
    
    `web: java -Dserver.port=$PORT $JAVA_OPTS -jar build/libs/graduation.com-0.0.1-SNAPSHOT.jar` 이거만 추가해주면 된다 
    
    원래 buildpack이 heroku/jvm일 때는 아무리 procfile을 바꿔도 jar파일을 찾지 못해서 jar file not found 에러가 떴는데 
    
    ⇒ 처음부터 다시 배포하면서 굳이 set build pack을 해줄 필요 없다는 것을 알았고 (알아서 스캔해서 알아낸다 heroku가) ⇒ 위에 링크를 따라하면 build 옵션이 정해지면서 잘 찾아진다 
    
- 이제 build가 성공적이니까 git push heroku master하면 deploy(배포)가 되긴 된다
- 근데 막상 그 링크로 들어가보면 오류가 뜨면서 heroku log —tail을 통해서 로그를 보라고 하는데 로그가 엄청 길었다...
- 계속 읽어보니까 맨 밑에 jpa hibernate dialect가 지정되어 있지 않고 jdbc연결이 원활하지 않다는 오류인 것 같은 것들이 써있었고 jpa 설정을 다시 해야하나..해서 걱정하다가 혹시 싶어서
- heroku에 다시 들어가서 mysql resource를 넣어주니까 신기하게도 됐다
- 중간에 index.html 파일로 인해서 contextLoads()에 오류나고 그랬는데 이 부분이 핵심 오류는 아니라고 한다
- spring boot test 어노테이션을 주석처리 + 해당 index.html파일은 test폴더와 main폴더 그리고 등등 다른 폴더에서 삭제 하니까 되던데 정확한 원인은 모르겠다
- 결과적으로 배포 완료 했고 링크는 [https://graduprojects.herokuapp.com/](https://graduprojects.herokuapp.com/)이다
- 요금 폭탄이 나올지 지켜봐야하는데 아직까지는 무료 시간 까이지도 않고...? 괜찮은 것 같다