# web-for-deploy

travis를 이용해 지속적인 통합과 자동 배포를 구현했습니다.


## CI에 관해서
<img width="1245" alt="스크린샷 2022-07-05 오전 1 34 45" src="https://user-images.githubusercontent.com/92219795/177193116-827c69ad-57d7-4d9f-bd79-72b56d292db6.png">

CI(Continuous Integration)는 지속적 통합이라 하여 코드 버전 관리 시스템에서 PUSH가 되면 자동으로 테스트와 빌드가 수행되는 안정적인 배포 파일을 만드는 과정이다. 

## 설정 방법

### Travis에서 repo 등록

1. GitHub에서 계정 권한 인증을 받는다.
2. 필요한 repo를 설정한다. 

### .travis.yml 작성 및 push

.travis.yml을 작성한다.

```yaml
language: java
jdk:
  - openjdk11

branches:<img width="1728" alt="스크린샷 2022-07-05 오전 1 37 01" src="https://user-images.githubusercontent.com/92219795/177193333-fc8b7721-72d7-4a54-bf0c-8c2badf4f80e.png">

  only:
    - main

cache:
  directories:
    - '$HOME/.m2/repository'

notifications:
  email:
    recipients:
      - rjsckdd12@gmail.com
```

### Travis에서 repo 마저 설정

설정하려는 repo를 들어가면 아래 그림처럼 나온다.
<img width="1050" alt="스크린샷 2022-07-05 오전 1 29 55" src="https://user-images.githubusercontent.com/92219795/177192604-b23ca94a-9c08-4dc8-adb4-7953e7978689.png">

**등록된 repo에서 Status Image를 설정한다.**

repo 이름 오른쪽에 보면 아래 버튼이 있는데 클릭해서. Status Image를 설정한다.

<img width="115" alt="스크린샷 2022-07-05 오전 1 30 30" src="https://user-images.githubusercontent.com/92219795/177192673-3fbb481c-4d84-4af8-bd5f-8fd24e349e00.png">


Format을 설정할 수 있는데, Image, Markdown 등 많은 포멧을 제공하고 있으니 필요한 것을 복사한다.

<img width="1120" alt="스크린샷 2022-07-05 오전 1 30 47" src="https://user-images.githubusercontent.com/92219795/177192705-36286447-b1ad-4749-8343-ef1e2a91a16e.png">

**원하는 방식을 선택해 해당 main 브랜치 readme 파일에 등록한다.**
 
복사한 내용을 readme 파일에 등록한다. **꼭 설정한 브랜치 readme 파일에 작성해야 한다.**

<img width="1728" alt="스크린샷 2022-07-05 오전 1 38 13" src="https://user-images.githubusercontent.com/92219795/177193465-bef8bb20-b9d4-4996-a507-350c3d794897.png">

**travis에 있는 Dashboard에서 `Trigger a build` 버튼을 누른다.**

<img width="1728" alt="스크린샷 2022-07-05 오전 1 37 24" src="https://user-images.githubusercontent.com/92219795/177193374-33894319-b6bc-4659-b6ad-2060a9ce5f98.png">

<img width="1010" alt="스크린샷 2022-07-05 오전 1 35 44" src="https://user-images.githubusercontent.com/92219795/177193220-040c0eb7-ca8b-419a-bc00-d7d4b9119046.png">



## 오류 상황

### 문제

maven으로 프로젝트를 등록할 떄, `.mvn/wrapper/maven-wrapper.properties` 를 찾지 못하는 경우가 있었다.
그래서 gitignore에 해당 파일을 깃에 등록하지 않아서 생긴 문제인줄 알았지만, 설정을 바꿔도 변하지 않았다.

### 대처

git가 래퍼 jar 파일을 제거하므로 빌드 중에 강제로 다운로드해야 한다고 한다. 

[해결 방법](https://github.com/travis-ci/travis-ci/issues/6298)으로 선택한 방법은 CI를 진행하면서 `mvn -N io.takari:maven:wrapper`와
`./mvnw install -DskipTests=true -Dmaven.javadoc.skip=true -B -V` 명령어를 install 하는 방식이었다.

```yaml
language: java
jdk:
  - openjdk11

install:
  - mvn -N io.takari:maven:wrapper
  - ./mvnw install -DskipTests=true -Dmaven.javadoc.skip=true -B -V

before_install:
  - chmod +x mvnw

branches:
  only:
    - main

cache:
  directories:
    - '$HOME/.m2/repository'

notifications:
  email:
    recipients:
      - rjsckdd12@gmail.com
```


## 마치면서

실제로 빌드는 성공했지만, 해당 몀령어들이 추가되어서 빌드 속도가 현저히 떨어지게 됐다. 이 방식이 아닌 제작한 로컬 레포에서 해당 wrapper 파일을 추가해 CI 빌드 속도를 빠르게 할 필요가 있어보인다.
