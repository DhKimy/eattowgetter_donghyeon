# ideaBANK -  eatTwoGetter



<img src="C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926164125677.png" alt="image-20220926164125677" style="zoom:50%;" />



**"공동 배달을 통해 배달요금을 분담하고 불필요한 지출을 줄인다."**

해당 repo는 "eatTwoGetter - 공유 배달을 위한 Java/Spring 기반 서비스 개발"에 대한 프로젝트 입니다. 



### 📣 개발목표

* 지도 기반의 게시판 웹사이트 구현
* 사용자들의 중간지점을 보여주는 서비스 도입
* 매칭이 된 사람기리 채팅할 수 있는 서비스 도입



### 📣구현 기능 및 기술

* 마커 형식의 게시판 구현
* GPS 신호 수신
* 중간 거리 찾기 
* 거리순으로 우리동네이웃 사람들 정렬
* 실시간 채팅 기능



### 📣개발환경

* IntelliJ
* GitHub



### 📣사용기술

#### - 백엔드

###### Spring boot

* JAVA 17
* Spring MVC
* Spring Boot Security
* Spring Web
* Lombok



###### Buid tool

* Gradle



###### Database

* MySQL Driver



###### AWS

* EC2



#### - 프론트엔드

* Javascript
* Thymeleaf



### 📣주요 키워드

* REST API
* 시큐리티
* 페이징
* Git 버전관리
* AWS EC2배포
* Kakao Map API
* Google G-mail API



### 📣내부 디렉터리 구조

```
├─java
│  │  
│  └─com
│      │ 
│      └─ll
│          │ 
│          └─example
│              │
│              └─getTwoGetter
│                  │  GetTwoGetterApplication.java
│                  │  Util.java
│                  │
│                  ├─Board
│                  │  ├─controller
│                  │  │      BoardController.java
│                  │  │
│                  │  ├─domain
│                  │  │  ├─entity
│                  │  │  │      Board.java
│                  │  │  │      BoardForm.java
│                  │  │  │
│                  │  │  └─repository
│                  │  │          BoardRepository.java
│                  │  │
│                  │  ├─dto
│                  │  │      BoardDistanceAsc.java
│                  │  │      BoardDto.java
│                  │  │      BoardDtoDistance.java
│                  │  │
│                  │  ├─model
│                  │  │      PageResult.java
│                  │  │
│                  │  └─service
│                  │          BoardService.java
│                  │
│                  ├─chat
│                  │  ├─controller
│                  │  │      ChatApiController.java
│                  │  │      ChatController.java
│                  │  │
│                  │  ├─dto
│                  │  │      ChatInfoDto.java
│                  │  │      ChatMessageDto.java
│                  │  │
│                  │  ├─model
│                  │  │      ChatInfo.java
│                  │  │      ChatMessage.java
│                  │  │
│                  │  ├─repository
│                  │  │      ChatInfoRepository.java
│                  │  │      ChatMessageRepository.java
│                  │  │
│                  │  └─service
│                  │          ChatInfoService.java
│                  │          ChatMessageService.java
│                  │
│                  ├─common
│                  │  └─constants
│                  │          BoardConstants.java
│                  │
│                  ├─exception
│                  │      DataNotFoundException.java
│                  │
│                  ├─login
│                  │  ├─config
│                  │  │      WebSecurityConfig.java
│                  │  │
│                  │  ├─controller
│                  │  │      AccountApiController.java
│                  │  │      AccountController.java
│                  │  │      HomeController.java
│                  │  │
│                  │  ├─model
│                  │  │      Role.java
│                  │  │      User.java
│                  │  │
│                  │  ├─Repository
│                  │  │      UserRepository.java
│                  │  │
│                  │  └─Service
│                  │          KakaoService.java
│                  │          MailService.java
│                  │          UserService.java
│                  │
│                  ├─middleMap
│                  │  └─Controller
│                  │          MiddlePlaceMapController.java
│                  │
│                  ├─Profile
│                  │      ProfileController.java
│                  │
│                  └─report
│                          reportController.java
│
└─resources
    │  application-prod.properties
    │  application.properties
    │
    ├─static
    │  │  .DS_Store
    │  │
    │  ├─css
	|  |  |
    │  │  └─images              
    │  │
    │  ├─images
    |  |
    │  │
    │  ├─js
    │  │  │  breakpoints.min.js
    │  │  │  browser.min.js
    │  │  │  category-marker.js
    │  │  │  jquery.min.js
    │  │  │  jquery.poptrox.min.js
    │  │  │  jquery.scrollex.min.js
    │  │  │  jquery.scrolly.min.js
    │  │  │  main-map.js
    │  │  │  main.js
    │  │  │  modal-board.js
    │  │  │  modal-category.js
    │  │  │  modal-chat.js
    │  │  │  register.js
    │  │  │  util.js
    │  │  │
    │  │  └─middlemap
    │  │          middleMap.js
    │  │
    │  ├─sass
    │  │  │
    │  │  ├─base
    │  │  │
    │  │  ├─components
    │  │  │
    │  │  ├─layout
    │  │  │
    │  │  └─libs
    │  │
    │  └─webfonts
    │
    └─templates
        │  index.html
        │
        ├─about
        │      about_us.html
        │
        ├─account
        │      find.html
        │      login.html
        │      register.html
        │
        ├─board
        │      edit_board.html
        │      list.html
        │      modal_board.html
        │
        ├─chat
        │      chatList.html
        │
        ├─fragment
        │      common.html
        │
        ├─mainMap
        │      map_wrap.html
        │      present_location.html
        │
        ├─middlePlaceMap
        │      middleMap.html
        │
        ├─profile
        │      myProfile.html
        │
        └─report
                userReport.html
```



### 📣시스템 구조

![image-20220927050723605](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220927050723605.png)

![image-20220927050738204](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220927050738204.png)

<hr>
### 📣결과물

##### ETG 사용방법 페이지

![image-20220926163302724](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163302724.png)

* 처음 접하는 사용자를 위한 소개페이지 입니다.  마지막 페이지에 있는 버튼을 클릭하면 메인 페이지로 이동합니다.



##### 메인페이지

![image-20220926143724565](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926143724565.png)

* 고객들이 생성한 마커를 확인할 수 있습니다.
* 마커를 생성하려면 로그인을 해야합니다.
* 원하는 음식 카테고리별로 마커를 확인할 수 있습니다.



##### 메인페이지 - 게시물 생성 

![image-20220926163154366](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163154366.png)

* 게시물을 생성하면 지정한 핀 위치에 마커가 띄워집니다.



##### 메인페이지 - 게시글 정보

![image-20220926163704086](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163704086.png)

* 마커를 클릭하면 게시글 정보를 확인할 수 있습니다.
* 본인이 생성한 마커이면 수정과 삭제를 할 수 있고, 본인이 아니라면 중간거리 확인과 채팅을 할 수 있습니다.



##### 로그인 페이지

![image-20220926163451828](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163451828.png)

* 로그인은 페이지 입니다.



##### 회원가입 페이지

![image-20220926163514172](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163514172.png)

* 회원가입 페이지에서는 닉네임 중복확인, 이메일 중복확인을 해야하도록 구현하였습니다.



##### 회원정보 수정 페이지

![image-20220926163610726](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163610726.png)

* 회원정보 수정페이지에서는 닉네임과 비밀번호가 수정이 가능하도록 하였고, 회원탈퇴 기능이 있습니다.



##### 채팅방 페이지

![image-20220926163358871](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163358871.png)

* 채팅페이지는 누군가 메세지를 보내면 알림이 뜨도록 구현하였습니다.
* 알림은 대화를 끝내고 창을 닫으면 사라집니다.



##### 우리동네 사람들 페이지

![image-20220926163420494](C:\Users\jjeon\AppData\Roaming\Typora\typora-user-images\image-20220926163420494.png)

* 본인 위치를 기준으로 가까운 동네사람들 부터 제일 먼 사람까지 확인이 가능한 페이지 입니다.



### 📣 시연영상

https://www.youtube.com/watch?v=i3RMGNMNdJk&feature=youtu.be



<hr>

### 📣추후 발전 과제

* 서비스 고도화
  * 프로필 사진 파일업로드 가능하도록 구현

