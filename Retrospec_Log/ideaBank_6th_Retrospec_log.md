팀 구성원 :
최태승(팀장),
이승훈,
김동현2,
박종수,
손정아

6주차 회고 내용 요약 (최소 500자 이상)

## 🔻GIT
아이디어 뱅크 – 깃 협업 규칙

Centralized Workflow

1.	Master 브랜치 하나만 사용. 자신의 로컬저장소에 변경내용 커밋하고 원격저장소와 동기화
2.	충돌이 발생한다면(팀원이 원격저장소의 커밋을 미리했다면) pull origin main(git fetch origin) 수행  (git status로 충돌한 파일을 확인하여 수동으로 해결)
3.	충돌이 해결 됐으면 push 실행.


Feature Branch Workflow

1.	원격저장소와 master브랜치를 사용. But master에 직접 커밋하지 않고 새로운 기능마다 브랜치를 만들어 작업.
2.	기능 브랜치에서 기능개발이 끝나면 풀 리퀘스트로 master에 직접 병합하는것이아니라 병합해 달라고 요청.
3.	즉, 자신이 많은일이 게시판 일이라면 게시판 브랜치에 푸쉬. git push -u origin 게시판
4.	풀 리퀘스트로 다른 팀원들이 확인 후 병합완료. (git checkout master / git fetch origin 게시판 / git merge origin 게시판 / git push origin master)

Gitflow Workflow

1.	Master, Develop 브랜치를 운영. Master는 변경이력 유지위해, Develop은 기능 브랜치 병합 목적.
2. 추후 논의필요


🔻 스프링부트에서 레디스를 외부 세션 저장소로 사용하기

[GitHub - woowang789/spring-redis-session at 68c261f061aa730dff349e6113d7e05d02048f7e](https://github.com/woowang789/spring-redis-session/tree/68c261f061aa730dff349e6113d7e05d02048f7e)

**서버 내에 세션을 저장**하는 웹을 베이스로 구현했습니다. 
(Spring security, h2, thymeleaf, JPA)

![스크린샷 2022-09-13 오후 10.48.23.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0fe713ea-08fc-4293-b0af-6298d9c2f8f3/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-13_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.48.23.png)

![스크린샷 2022-09-13 오후 10.49.46.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/02bcf3c4-b734-4486-a7d2-333171e10a17/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-13_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.49.46.png)

구현된 기능

- 회원가입
- 로그인
- 로그아웃
- + 접근 권한

지금처럼 서버 내에 세션을 저장하게 되면 몇 가지 문제가 발생합니다. 

![스크린샷 2022-09-13 오후 11.17.50.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bcdfb045-ec18-4935-98cd-be78a7483586/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-13_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.17.50.png)

![스크린샷 2022-09-13 오후 11.18.03.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/041f4197-ddef-4ba7-86df-5f0a060297be/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-13_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.18.03.png)

1. 서버가 종료되면 메모리에 저장된 세션이 모두 날아가 모든 회원이 로그아웃 됩니다. 
2. WAS가 2개 이상 뜰 경우, 두 WAS 간 동기화가 쉽지 않습니다.
로드밸런서가 A 서버에 로그인 한 유저를 B 서버로 보내는 경우 그 유저는 다시 로그인해야 됩니다.

이 문제를 레디스를 **외부 세션 저장소**로 사용해 해결할 수 있습니다.

![스크린샷 2022-09-13 오후 11.18.26.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b0149656-cca4-48ab-a865-ac53818ad9e8/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-13_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.18.26.png)

### Redis

Java의 Redis Client는 Jedis, Lettuce 이렇게 2가지가 있습니다.
Jedis는 사실상 java 기반 응용프로그램 표준 드라이버이고 많이 사용함에도 Spring에선 Lettuce를 선호합니다.
Jedis는멀티쓰레드 환경에서 안전하지 않기 때문에 스프링에서 제공하는 `LettuceConnectionFactory`  구현체를 사용하는 것이 좋습니다.

🔻 도커🐋

리눅스의 응용 프로그램들을 소프트웨어 컨테이너 안에 자동으로 배치하기 위한 오픈소스 프로젝트

 >> 컨테이너들을 쉽게 관리하고 신속하게 배포 및 확장이 가능하게 한다. 

- 컨테이너
    
    : 애플리케이션과 애플리케이션이 동작하기 위해 필요한 라이브러리나 모듈 등을 묶어준 것. 
    
    > 애플리케이션과 애플리케이션을 구동하는 환경을 컨테이너 박스에 넣고 하나로 만든 것.
    
    >> 컨테이너를 옮기면 안에 넣었던 소프트웨어들이 모두 함께 움직이기 때문에 하나하나 
    
          관리 할 필요가 없다. 
    

- 이미지

     : 서비스 운영에 필요한 서버 프로그램, 소스코드 및 라이브러리, 컴파일 된 실행 파일을 묶는 형태

 컨테이너 생성에 필요한 모든 파일과 설정값 ( 환경 ) 을 지닌 것으로, 더 이상의 의존성 파일을 컴  

 파일 하거나 설피할 필요가 없는 상태의 파일을 말 한다.

 예를 들어 Ubuntu이미지는 Ubuntu를 실행하기 위한 모든 파일을 가지고  있다. 

- 초기화 명령 스크립트
    
    ```
    # 컨테이너 삭제
    docker rm -f $(docker ps -qa)
    
    # 이미지 삭제
    docker rmi -f $(docker images -qa)
    
    # 안쓰는 네트워크 삭제
    docker network prune -f
    
    # 안쓰는 볼륨 삭제
    docker volume prune -f
    
    # 도커 프로젝트 삭제
    sudo rm -rf /docker/projects
    sudo rm -rf /docker_projects
    ```
    

 

# 2.파일 업로드

## 회원 정보 얻기

### 1. Controller에서 Principal 객체에 접근

```java
@Controller
@RequiredArgsConstructor
public class HomeController {
  
    @PreAuthorize("isAuthenticated()")
    @GetMapping("/currentUserOrigin")
    @ResponseBody
    public Principal currentUserOrigin(Principal principal) {
        return principal;
    }
```

- 사용 가능한 메서드가 거의 getName()밖에 없어서 사용자 정보의 극히 일부만 가져올 수 있다.

### 2. @AuthenticationPrincipal

  Principal은 JAVA 표준 Principal 객체이기 때문에 name 정보밖에 불러오지 못 한다.

 > @AuthenticationPrincipal 애노테이션을 사용하면 UserDetailsService에서 return하는 객체를     

     직접  파라미터로 받아서 사용할 수 있다. 

```java
public class MemberContext extends User {
    private final Long id;
    private final String profileImgUrl;

    public MemberContext(Member member, List<GrantedAuthority> authorities) {
        super(member.getUsername(), member.getPassword(), authorities);
        this.id = member.getId();
        this.profileImgUrl = member.getProfileImgUrl();
    }

/**********************************************************************************/

@GetMapping("/currentUser")
    @ResponseBody
    public MemberContext currentUser(**@AuthenticationPrincipal** MemberContext memberContext) {
        return memberContext;
    }
```

✔User클래스를 상속받는 이유

 >  UserDetailsService에서 Return하는 객체는 UserDetails 타입이어야 하기 때문에 

UserDetails를 구현하는 User클래스를 상속하는 방식으로 사용한다.

## 도커-(docker 명령어)

```bash
docker rmi -f $(docker images -qa) # 기존 *모든 컨테이너 삭제

sudo mkdir -p /docker_projects/nginx_1/volumes # 도커 볼륨 폴더 생성

docker run -d -p 8031:80 --name=nginx_1 --rm -v /docker_projects/nginx_1/volumes/usr/share/nginx/html:/usr/share/nginx/html nginx # 컨테이너 생성
-> port 8031:80  , name = nginx_1 , 
-> /docker_projects/nginx_1/volumes/usr/share/nginx/html 
-> /usr/share/nginx/html 
-> 위 둘의 폴더를 연결하겠다
sudo chown lldj: -R /docker_projects # 권한부여
```

```bash
-> docker 명령어가 안먹힐 시 방화벽 확인하기
sudo systemctl stop firewalld

sudo systemctl disable firewalld
```

<aside>
🛠 Dockerfile == docker에서 이용하는 이미지를 기반으로 하여 새로운 이미지를 
스크립트 파일을 통해 내가 설정한 나만의 이미지를 생성할 수 있는 일종의 이미지 설정파일

</aside>

```bash
# Dockerfile 

# 최신 node 이미지로 부터 시작
FROM node

# Working Directory 지정
# 도커 컨테이너의 작업폴더를 지정
WORKDIR /usr/src/app

# COPY package.json ./
# 앞의 ./는 HOST OS의 현재 폴더를 의미
# 뒤의 ./는 컨테이너의 현재 폴더(WORKDIR)를 의미
# 즉 외부에서 만들어둔 package.json 파일을 컨테이너 내부로 복사하겠다는 의미
COPY ./ ./

# node의 종속성 다운로드
# RUN 명령어는 컨테이너에서 실행
RUN npm install

# 안해도 되지만, 하는게 좋습니다.
# 이 컨테이너는 8080 포트를 사용한다는 의미 입니다.
EXPOSE 8080

# docker run 명령에서 실행항 명령이, 이 명령어 부터는 제작타임이 아닌 런타임에서 실행됨
CMD ["node", "index.js"]
```

```bash
docker build -t docker_image_name
```

## 스프링부트(파일 업로드 프로젝트)

- @AuthenticationPrincipal
    
    >> 로그인한 사용자의 정보를 파라미터로 받고 싶을 때 기존에는 Principal 객체로 받아서 사용한다. 
    
    - 코드
        
        ![스크린샷 2022-09-16 오전 1.38.19.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0c000167-90d5-4390-819c-9eae60bbc8a3/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-16_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_1.38.19.png)
        
    
    >> 하지만 JAVA 표준 Principal 객체이며 우리가 참조할수 있는 정보는 name 정보 밖에 없다
    
    >> @AuthenticationPrincipal 애노테이션을 사용하면 **Service에서 Return한 객체**를 파라미터로 직접 받아 사용할 수 있다.
    
    - 코드



## 팀원 주요 이슈
8월 30일(화) 팀원 이승훈님이 컴퓨터에 깔아놓은 알약 프로그램의 장애로 부팅이 되지 않는 현상을 겪었다. 팀원들이 OS 지식과 보안 지식을 풀가동하여 1시간 10분 만에 이승훈님의 컴퓨터를 정상으로 돌려놓는 것에 성공하였다.

이를 계기로 OS와 보안 프로그램 사이의 관계에 대해 조금 더 자세히 알아보았다.

Window OS svchost.exe process
8월 30일 윈도우의 개인용 ‘알약' 프로그램이 독약으로 변하여 부팅이 되지 않게 하는 오류가 일어났다. 많은 사람들이 이 원인에 대해 모르고 잘못 대처하는 바람에 하지 않아도 될 컴퓨터 초기화를 무리하여 진행하기도 했다.

간단하게 이야기하면, 알약을 사용하여서 랜섬웨어에 감염이 될 우려가 있는 것이 아니라, svchost.exe 라는 윈도우 프로세스를 알약이 랜섬웨어로 감지하여 작동하지 못하게 한 것인데, 이것이 왜 OS를 띄우지 못하게 했을까?

svchost.exe 에 대해 알아보고, 개발자 꿈나무로서 알아야 할 OS 관련 보안 지식에 대해 간략히 알아보고자 한다.

1. svchost.exe
1) svchost.exe란?

서비스 호스트라고 불리기도 하는데, 하나 이상의 윈도우 서비스를 호스팅하는 프로세스이다. 다양한 서비스를 하나의 프로세스로 그룹화해서 리소스 사용량을 줄이는 역할을 한다.

svchost.exe가 실행되는 이유는 윈도우 구성 요소 중 많은 DLL(Dynamic Link Library) 파일들이 있는데, DLL파일은 exe처럼 홀로 실행 되지 않기 때문이다. svchost.exe가 DLL 파일들을 OS에 연결하여 효과적으로 관리한다.

svchost.exe 프로세스는 램을 많이 사용하며 여러 개의 프로세스가 실행되기 때문에 악성 프로세스로 의심될 수 있다. 하지만 svchost.exe는 하나의 작업에 여러 서비스를 호스팅하고 있기에 다른 프로세스보다 svchost 메모리 사용량이 높을 수도 있다.

2) 사용 예시

네트워크 관련 서비스마다 별도의 프로세스가 실행되면 효율성과 편의성이 떨어진다. 이때 svchost.exe는 모든 서비스를 하나의 프로세스로 그룹화하며, svchost.exe는 방화벽, 윈도우 업데이트, 블루투스 지원, 네트워크 연결 등의 서비스를 그룹화하여 컴퓨터의 자원을 절약하는 역할을 담당한다.

3) svchost.exe를 위장한 악성 프로그램

svchost.exe는 윈도우 운영체제에 꼭 필요한 프로그램이다. 실제 svchost.exe 파일은 제거 자체가 불가능하다. 그러나 프로세스가 실행될 때 생성되는 svchost.exe로 위장하는 프로그램이 다수 있다. 본인이 실행한 프로세스와 그와 연관되어 실행되는 svchost.exe를 찾아보고, 관련이 전혀 없는 svchost.exe는 반드시 삭제해주어야 한다.

2. OS 관련 필수 보안 지식
1) 기본에 충실하자.

흔히들 하는 이야기가 있다. 확인되지 않는 파일 또는 메일을 확인하지 말라는 것. exe, macro 파일 등은 신뢰되지 않는 파일이라면 열지 않는 것이 좋겠다.

또한 OS가 기본으로 제공하는 방화벽, 바이러스 검사 툴을 활용하여 일상에서 외부 공격이 있을 때 잘 막아낼 수 있도록 환경설정을 하는 것이 중요하다.

2) 권한 부여에 신중하기

다른 프로그램이 권한을 필요로 하는 것들이 있다. 악성 프로그램을 모르고 실행하고 권한을 부여하는 경우, 의존성이 역전하는 것 마냥 내 컴퓨터가 내것이 아니게 된다. 그러므로 어떤 권한을 누구에게 넘겨 주는지, 프로그램을 실행할 때는 신중하게 생각하고 프로그램을 돌려야 한다.

3) 개발 시 보안

내 컴퓨터 보안과는 거리가 멀지만, 프로그램을 만들 때 생각해야 하는 보안이다. 시큐어 코딩이라고도 하는데, 인터넷 홈페이지나 소프트웨어 개발 시에 보안 취약점을 활용한 공격으로부터 안전하게 방어할 수 있도록 코딩하는 것이다. 초기 개발자라면 시큐어 코딩을 공부하고 본인의 작고 소중한 프로그램에 적용을 해보는 것도 보안을 익혀가는데 좋은 방법일 것이다.


팀 자랑 사진
KakaoTalk_Photo_2022-09-02-17-33-18


이번 주 수업을 마치며..
김동현2 : 인프라 수업이 재미있다. 얼른 실제로 배포를 할 수 있는 것을 알아봐야 겠다.
이승훈 : 이번주 프로젝트와 강의를 병행하면서 놓치는 점도 많았지만, 한계를 넘어가는 것을 느꼈다. light weight!
손정아 : 깃허브 협업을 배워서 아주 도움이 되었다.
박종수 : 주말을 이용해서 부족한 것을 채우고 계획했던 것을 완성하는 절차를 밟아보고 싶다.
최태승 : 8월도 고생 많았다. 나 자신.
