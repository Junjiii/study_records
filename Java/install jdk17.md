# Java 17 설치 및 IntelliJ 버전 변경

<br/>
<br/>

## 기록 배경

spring 기초 강의를 보기 위해 인프런의 김영한 님의 강의를 보기 시작했다.

해당 강의의 공지사항에 24년 6월 기준 spring boot 3.0 이상 설치 , jdk 17 버전 이상 업데이트

h2 데이터베이스 1.4.200버전 설치 의 내용이 있었다.

현재 나는 java 문법 공부를 위해 jdk 8을 설치되어 있는 상황이고

jdk17버전 업데이트 상황과 마주한 문제들을 리마인드를 위해 기록하게 되었다.
<br/>
<br/>
<br/>

## JDK 17 설치

<br/>

```markdown
brew install openjdk@17
```

<br/>

![jdk 17 설치 .png](https://github.com/Junjiii/study_records/assets/138355289/3ee7f539-ca8d-4083-9497-51bb8960d3a3)

<br/>

설치는 homebrew 를 통해 해당 명령어를 입력하고 아래와 같은 약 3분 정도의 설치 시간이 소요되었다.

(appcast 관련 에러는 왜 나오는지 아직도 모르겠다… )

<br/>
<br/>
<br/>

## 버전 확인 및 설치 목록 확인

<br/>

```markdown
// java 버전 확인 명령어
java -version

//java 설치 목록 확인 명령어
/usr/libexec/java_home -v
```

<br/>
<br/>

![Java version 확인](https://github.com/Junjiii/study_records/assets/138355289/6fcae066-4213-4679-af24-e89edc6bba6e)

jdk17 설치 후 설치된 목록을 확인해보니 17버전이 나타나지를 않았다.
<br/>
<br/>
<br/>

## 설치 경로
<br/>

```markdown
// homebrew 설치 리스트 확인
brew list

// homewbrew 설치 경로 확인
brew --prefix (패키지 ex: openjdk@17)
```

<br/>
검색을 해보니 설치 목록 확인 명령어는 기본적으로 '/Library/Java/Javavirtualachin 경로를 통해 설치된 패키지를 탐색한다. 그런데 jdk 17버전은 기존 설치 경로와는 다른 경로에 설치 되어 해당 명령어로는 설치된 여부를 확인 할 수 없었다.

따라서
<br/>

![brew list 확인](https://github.com/Junjiii/study_records/assets/138355289/ea617883-8ccf-4c22-8c90-d37cf84eeaec)
<br/>
먼저 jdk17 이 설치가 되었는지 부터 확인했고 설치는 되어있었다.

그 후
<br/>

![jdk 설치 경로 알아내기 ](https://github.com/Junjiii/study_records/assets/138355289/324c5682-1dd9-479e-b1a0-8e3eece5f642)
<br/>

경로가 /usr/local/opt/openjdk@17 에 설치된 것을 확인했다.
<br/>
<br/>
<br/>

## 심볼릭 링크
<br/>

```markdown
// 심볼릭 링크 명령어
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```
<br/>
두 가지를 시도해보았다. 다른 곳에 설치된 jdk17패키지 디렉토리를 복사하여 /Library/Java/JavaVirtualMachines 경로에 붙여넣기 하는 방법을 시도해보았으나 에러가 많이 뜨면서 접근자 제어 등의 문제로 실패 했다.

다른 방법으로 “심볼릭 링크” 라고 하여 기존에 탐색하도록 설정되어있는 경로를 상징적으로? 가상의 파일과 현재 링크 시키려는 디렉토리의 파일을 링크 시켜서 openjdk.jdk 를 찾아내면 자연스럽게 /opt/homebrew/opt/openjdk@17 이 경로를 탐색할 수 있게 만들어 주는 방법이였다.

하지만 실패하였고 일단은 설치는 되어있음을 확인하고 다음 작업을 진행하기로 했다.
<br/>
<br/>
<br/>

## 경로 설정
<br/>

```markdown
// 편집 명령어
vi ~/-zshrc

// 추가해주어야할 내용

# Java 17

export JAVA_HOME_17="/usr/local/opt/openjdk@17/libexec/openjdk.jdk/Contents/Home"
export PATH=$JAVA_HOME_17/bin:

// # 을 붙이면 주석 같이 추가한 내용이 반영이 안된다.
// JAVA_HOME_17 이라는 변수를 openjdk@17 이 설치된 경로로 할당해준다.
// PATH 를 통해 프로젝트 실행시 JAVA_HOME_17 이라는 경로로 이동해 실행시킨다 라는 내용의 명령어들이다.
// 해당 명령어를 .zshrc 에 그대로 붙여넣어주었다.
// 개인마다 작업했던 프로젝트들이 다양하게 있을수도 있는데 그 프로젝트마다 사용된 버전이 다르다보니 상황에 맞춰
// 경로를 바꿔주면서 해당하는 프로젝트를 다시 실행시키면 된다.
```

<br/>
새로운 버전을 설치한 경우 IntelliJ 와 같은 IDE 를 실행시켰을때 설정 되어있는 경로를 따라 jdk 버전을 확인 후 실행된다. 그것을 새로운 버전으로 실행시키기 위해 편집기를 통해 경로 설정이 되어있는 파일에 위의 내용을 추가 해주어야 하는데 mac 기준 catalina 버전부터는 기본 편집기가 .bash 에서 .zsh 로 바뀌었다.

따라서 sonoma 14.5 버전에서는 zsh 이기 때문에 .zshrc 라는 파일을 vi 와 같은 편집기 명령어를 사용해서 추가해주었다.
<br/>

![zshrc 경로 ](https://github.com/Junjiii/study_records/assets/138355289/3d216f25-5d77-4af8-af4e-eebe4eb0f54e)

<br/>

![경로 설정](https://github.com/Junjiii/study_records/assets/138355289/56a4be01-57f0-4894-b0b4-a20996f12e57)

<br/>
이 후 마지막으로 버전 확인을 해보았다.
<br/>

![최종 버전 확인](https://github.com/Junjiii/study_records/assets/138355289/c57c0965-bb07-4c9f-a781-eafe464431f8)

<br/>

결과는 성공!

이제부터는 스프링3.0 버전을 사용할 수 있게 되었다.
<br/>
<br/>
<br/>

## IntelliJ 프로젝트에 jdk17 버전 바꾸기

<br/>
기존에 작업하던 프로젝트가 있는 경우 중 jdk17 버전으로 업데이트하고 싶다면 이렇게 해보자 .
<br/>

1. intellij 화면
<br/>

![인텔리 제이 화면](https://github.com/Junjiii/study_records/assets/138355289/a94c1133-f1c2-4d83-b6f8-8467bd6b7cbe)
먼저 intelliJ 로 들어온다.
<br/>

2. project signature 를 누른다.
<br/>

![project signature ](https://github.com/Junjiii/study_records/assets/138355289/6abfdd40-8c50-4f2f-b69a-8e6f22adb991)
<br/>

4.  add jdk17 버전을 해준다.
<br/>

![add jdk17](https://github.com/Junjiii/study_records/assets/138355289/fa81c3c1-dce7-4d36-b23d-788787d0cdc4)

<br/>
    이미 추가를 해버려서 사진이 없지만 버전을 새로 설치한 후에는 Add JDK… 이라는 버튼 아래에

    add “openjdk@17” 이 있었다. 버튼을 누르면 사진과 같이 jdk_17이 추가된다.
<br/>

4.  project 로 가서 이전에 추가한 17버전을 눌러 수정해주고 apply 해준다.
<br/>

![스크린샷 2024-06-21 오후 3 44 11](https://github.com/Junjiii/study_records/assets/138355289/b3559e91-1439-4e42-b34b-c972ded264e7)

<br/>

5.  modlues 에 default 로 바꿔준다.
<br/>

![스크린샷 2024-06-21 오후 3 46 05](https://github.com/Junjiii/study_records/assets/138355289/1af878d7-0326-4946-8464-eaa23f4e0c19)

<br/>

![스크린샷 2024-06-21 오후 3 46 40](https://github.com/Junjiii/study_records/assets/138355289/6dd5c993-0195-42a9-8224-579b3d523227)

<br/>
<br/>
<br/>

##마무리

이렇게 해주면 jdk17 버전 설치 및 기존 프로젝트에 적용시킬 수 있다.
