# Java 17 설치 및 IntelliJ 버전 변경

## 기록 배경

spring 기초 강의를 보기 위해 인프런의 김영한 님의 강의를 보기 시작했다.

해당 강의의 공지사항에 24년 6월 기준 spring boot 3.0 이상 설치 , jdk 17 버전 이상 업데이트

h2 데이터베이스 1.4.200버전 설치 의 내용이 있었다.

현재 나는 java 문법 공부를 위해 jdk 8을 설치되어 있는 상황이고

jdk17버전 업데이트 상황과 마주한 문제들을 리마인드를 위해 기록하게 되었다.

## JDK 17 설치

```markdown
brew install openjdk@17
```

![jdk 17 설치 .png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/8d11bedf-c843-4252-97f4-b1fc1efead7f/jdk_17_%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5_.png)

설치는 homebrew 를 통해 해당 명령어를 입력하고 아래와 같은 약 3분 정도의 설치 시간이 소요되었다.

(appcast 관련 에러는 왜 나오는지 아직도 모르겠다… )

## 버전 확인 및 설치 목록 확인

```markdown
// java 버전 확인 명령어
java -version

//java 설치 목록 확인 명령어
/usr/libexec/java_home -v
```

![Java version 확인.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/700642ed-279b-4c8c-bb83-cc60cc60d0a0/Java_version_%E1%84%92%E1%85%AA%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB.png)

jdk17 설치 후 설치된 목록을 확인해보니 17버전이 나타나지를 않았다.

## 설치 경로

```markdown
// homebrew 설치 리스트 확인
brew list

// homewbrew 설치 경로 확인
brew --prefix (패키지 ex: openjdk@17)
```

검색을 해보니 설치 목록 확인 명령어는 기본적으로 '/Library/Java/Javavirtualachin 경로를 통해 설치된 패키지를 탐색한다. 그런데 jdk 17버전은 기존 설치 경로와는 다른 경로에 설치 되어 해당 명령어로는 설치된 여부를 확인 할 수 없었다.

따라서

![brew list 확인.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/47812107-cfed-4219-81d0-e71c2f8bd476/brew_list_%E1%84%92%E1%85%AA%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB.png)

먼저 jdk17 이 설치가 되었는지 부터 확인했고 설치는 되어있었다.

그 후

![jdk 설치 경로 알아내기 .png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/383957a2-d83b-4ebf-af32-82144852db9e/jdk_%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5_%E1%84%80%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A9_%E1%84%8B%E1%85%A1%E1%86%AF%E1%84%8B%E1%85%A1%E1%84%82%E1%85%A2%E1%84%80%E1%85%B5_.png)

경로가 /usr/local/opt/openjdk@17 에 설치된 것을 확인했다.

## 심볼릭 링크

```markdown
// 심볼릭 링크 명령어
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

두 가지를 시도해보았다. 다른 곳에 설치된 jdk17패키지 디렉토리를 복사하여 /Library/Java/JavaVirtualMachines 경로에 붙여넣기 하는 방법을 시도해보았으나 에러가 많이 뜨면서 접근자 제어 등의 문제로 실패 했다.

다른 방법으로 “심볼릭 링크” 라고 하여 기존에 탐색하도록 설정되어있는 경로를 상징적으로? 가상의 파일과 현재 링크 시키려는 디렉토리의 파일을 링크 시켜서 openjdk.jdk 를 찾아내면 자연스럽게 /opt/homebrew/opt/openjdk@17 이 경로를 탐색할 수 있게 만들어 주는 방법이였다.

하지만 실패하였고 일단은 설치는 되어있음을 확인하고 다음 작업을 진행하기로 했다.

## 경로 설정

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

새로운 버전을 설치한 경우 IntelliJ 와 같은 IDE 를 실행시켰을때 설정 되어있는 경로를 따라 jdk 버전을 확인 후 실행된다. 그것을 새로운 버전으로 실행시키기 위해 편집기를 통해 경로 설정이 되어있는 파일에 위의 내용을 추가 해주어야 하는데 mac 기준 catalina 버전부터는 기본 편집기가 .bash 에서 .zsh 로 바뀌었다.

따라서 sonoma 14.5 버전에서는 zsh 이기 때문에 .zshrc 라는 파일을 vi 와 같은 편집기 명령어를 사용해서 추가해주었다.

![zshrc 경로 .png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/600ff4d4-3ef1-44f8-af56-5bb38e353a9b/zshrc_%E1%84%80%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A9_.png)

![경로 설정.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/10a7be66-9eaa-4622-83fc-d15a8f0948d8/%E1%84%80%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A9_%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8C%E1%85%A5%E1%86%BC.png)

이 후 마지막으로 버전 확인을 해보았다.

![최종 버전 확인.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/16cf1d46-2ad1-4be2-ab9d-6070e806a0f7/%E1%84%8E%E1%85%AC%E1%84%8C%E1%85%A9%E1%86%BC_%E1%84%87%E1%85%A5%E1%84%8C%E1%85%A5%E1%86%AB_%E1%84%92%E1%85%AA%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB.png)

결과는 성공!

이제부터는 스프링3.0 버전을 사용할 수 있게 되었다.

## IntelliJ 프로젝트에 jdk17 버전 바꾸기

기존에 작업하던 프로젝트가 있는 경우 중 jdk17 버전으로 업데이트하고 싶다면 이렇게 해보자 .

1. intellij 화면

![인텔리 제이 화면.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/1ef5dd65-6a2d-4478-8297-0f1ba04a4edf/%E1%84%8B%E1%85%B5%E1%86%AB%E1%84%90%E1%85%A6%E1%86%AF%E1%84%85%E1%85%B5_%E1%84%8C%E1%85%A6%E1%84%8B%E1%85%B5_%E1%84%92%E1%85%AA%E1%84%86%E1%85%A7%E1%86%AB.png)

먼저 intelliJ 로 들어온다.

2. project signature 를 누른다.

![project signature .png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/ae83f895-631a-4a5f-8a01-81d34264af88/project_signature_.png)

3.  add jdk17 버전을 해준다.

    ![add jdk17.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/7e229dd8-994a-4498-801c-053282b94f63/add_jdk17.png)

    이미 추가를 해버려서 사진이 없지만 버전을 새로 설치한 후에는 Add JDK… 이라는 버튼 아래에

    add “openjdk@17” 이 있었다. 버튼을 누르면 사진과 같이 jdk_17이 추가된다.

4.  project 로 가서 이전에 추가한 17버전을 눌러 수정해주고 apply 해준다.

        ![스크린샷 2024-06-21 오후 3.44.11.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/4719687e-f085-4fe0-9aa0-84971103f177/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-06-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.44.11.png)

5.  modlues 에 default 로 바꿔준다.

        ![스크린샷 2024-06-21 오후 3.46.05.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/d693bfb7-ad68-4fb3-86c1-40c478d3b1fc/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-06-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.46.05.png)

        ![스크린샷 2024-06-21 오후 3.46.40.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/5ab616dd-882c-45af-94f3-90bdca3d329f/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-06-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.46.40.png)

##마무리
이렇게 해주면 jdk17 버전 설치 및 기존 프로젝트에 적용시킬 수 있다.
