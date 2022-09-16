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



**서버 내에 세션을 저장**하는 웹을 베이스로 구현했습니다. 
(Spring security, h2, thymeleaf, JPA)



구현된 기능

- 회원가입
- 로그인
- 로그아웃
- + 접근 권한

지금처럼 서버 내에 세션을 저장하게 되면 몇 가지 문제가 발생합니다. 



1. 서버가 종료되면 메모리에 저장된 세션이 모두 날아가 모든 회원이 로그아웃 됩니다. 
2. WAS가 2개 이상 뜰 경우, 두 WAS 간 동기화가 쉽지 않습니다.
로드밸런서가 A 서버에 로그인 한 유저를 B 서버로 보내는 경우 그 유저는 다시 로그인해야 됩니다.

이 문제를 레디스를 **외부 세션 저장소**로 사용해 해결할 수 있습니다.



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
        
     
        
    
    >> 하지만 JAVA 표준 Principal 객체이며 우리가 참조할수 있는 정보는 name 정보 밖에 없다
    
    >> @AuthenticationPrincipal 애노테이션을 사용하면 **Service에서 Return한 객체**를 파라미터로 직접 받아 사용할 수 있다.
    
    - 코드



## 팀원 주요 이슈






## 이번 주 수업을 마치며..

김동현2 : 

이승훈 : 

손정아 : 

박종수 : 고객센터 구현을 마무리 짓기위해 지난 수업을 다시 복습하는중인데 

최태승 :
