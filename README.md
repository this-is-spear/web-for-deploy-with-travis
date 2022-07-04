# web-for-deploy
[![Build Status](https://app.travis-ci.com/this-is-spear/web-for-deploy.svg?branch=main)](https://app.travis-ci.com/this-is-spear/web-for-deploy)

travis를 이용해 지속적인 통합과 자동 배포를 구현했습니다.


## CI에 관해서

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

### Travis에서 repo 마저 설정

1. 등록된 repo에서 Status Image를 설정한다.
2. 원하는 방식을 선택해 해당 main 브랜치 readme 파일에 등록한다.
3. travis에 있는 Dashboard에서 `Trigger a build` 버튼을 누른다.

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
