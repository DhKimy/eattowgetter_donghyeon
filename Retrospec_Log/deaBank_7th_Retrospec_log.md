팀 구성원 : 

이승훈,
김동현2(팀장),
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


🔻 ## The driver has not received any packets from the server. 오류 해결(mysql 완전 재설치)

### 1. 문제의 발생

AWS를 활용하여 도커 배포를 실험을 한창 하고,,, 그다음 날. 인텔리제이를 켜고 평소처럼 localhost:8080의 세상으로 들어가려는 순간. 다음과 같은 오류를 만났다.

```
com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure

The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.
    at com.mysql.cj.jdbc.exceptions.SQLError.createCommunicationsException(SQLError.java:174) ~[mysql-connector-java-8.0.29.jar:8.0.29]
    at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:64) ~[mysql-connector-java-8.0.29.jar:8.0.29]

    ....

```

패킷을 보냈지만 드라이버가 패킷을 받지 못하는... 흔히 말하는 연결 오류가 발생하였다. 하지만 나는 mac 로컬에서는 특별히 무엇인가를 삭제하거나 이동시킨 것이 없고, UTM이랑 AWS에서 작업을 한 것 밖에 없는데 뭐 때문에 꼬여버렸는지 몰랐다.

그래서 터미널을 켜고 mysql이 구동되는지를 알아보았다.

```
ERROR 2002 (HY000) : Can’t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock’ (2)
```

처음 보는 에러였다. 뭐 자세히 보니 소켓 파일이 연결되지 않는 문제인 거 같은데, 그러면 거기에 파일이 없거나, 아니면 있지만 인식을 못하거나, 불명의 원인으로 그것을 인식하지 못하는 것일 것이라는 생각을 하였다.

여러 블로그들을 참고하여 mysql.sock 위치를 찾아보기도 하고, 경로를 심볼릭링크로 설정하기도 하였다.

mysql.sock 위치는 아래의 명령어로 찾았다.

```
mysql_config --socket
```

var/lib/mysql 경로에 mysql.sock 파일이 없다는 것을 알 수 있었다.확인을 해보니 없는 것 같아서 새로 폴더를 만들어서 sock 파일을 넣어주기도 했는데, 똑같은 오류는 계속 떴다.

brew를 사용해서 mysql을 재설치를 해보기도 하고, my.cnf 파일을 지우고 새로 받아도 보았지만, 결국 되지 않았다.

한 세 시간 쯤 찾았을까 그냥 포맷을 시키는 정도로 mysql의 흔적을 없애고 다시 설치하면 되지 않을까 하는 생각이 들었다.

AWS 연결을 인텔리제이에서 시도를 하긴 했으니까... 그래서 아주 깊은 삭제, 설치를 해보기로 하였다.

### 2. Mysql 깊은 삭제와 재설치

다음과 같은 절차를 따르면서 재설치를 진행하였다.

### 1) MySQL 프로세스 죽이기

나는 홈브루로 설치를 해서 아래와 같은 방법을 사용하였다.

(homebrew로 설치했을 경우)

```
brew services stop mysql
```

(launchctl을 등록했다면 내리기)

```
sudo launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

### 2) 관련 파일 삭제하기

(1) 설치 경로 확인하기

```
which mysql
/usr/local/bin/mysql
```

(2) homebrew로 삭제하기

```
brew uninstall --force mysql
```

혹은

```
brew uninstall mysql --ignore-dependencies
brew remove mysql
brew cleanup
```

(3) 다음의 라인을 한 줄씩 입력해서 삭제한다.

```
sudo rm -rf /usr/local/mysql
sudo rm -rf /usr/local/bin/mysql
sudo rm -rf /usr/local/var/mysql
sudo rm -rf /usr/local/Cellar/mysql
sudo rm -rf /usr/local/mysql*
sudo rm -rf /tmp/mysql.sock.lock
sudo rm -rf /tmp/mysqlx.sock.lock
sudo rm -rf /tmp/mysql.sock
sudo rm -rf /tmp/mysqlx.sock
sudo rm ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
sudo rm -rf /Library/StartupItems/MySQLCOM
sudo rm -rf /Library/PreferencePanes/My*
```

(4) 완전 삭제한 이후 컴퓨터를 재부팅한다.

### 3) homebrew로 재설치하기

```
brew install mysql
```

### 4) mysql 서비스 시작하기

```
brew services start mysql
```

### 5) 비밀번호 없이 root 로그인

```
mysql -uroot
```

여기서 문제가 있었는데, 나의 경우, 비밀번호가 살아있었다... 이전에 설정한 root 비밀번호가 살아있었는데, 이유는 모르겠다.

### 6) root 비밀번호 설정하기

```
mysql_secure_installation
```

=> VALIDATE PASSWORD PLUGIN 설치 여부 물어볼 때는 N(아니오)를 선택할 것.

위의 절차를 따라하니, 정상적으로 스프링 부트 프로젝트가 로컬에서 돌아갔다. AWS ec2, rds 뭐 다 끄고 난리였지만, 완전 삭제, 재설치부터 해보는 습관을 들여야겠다는 생각을 할 수 있었다.

<참고>

> https://github.com/rangyu/TIL/blob/master/mysql/MySQL-완전-삭제하고-재설치하기-(MacOS).md
>


# Batch

## ❗️ 개념

**상호작용이 필요한가**

Y → 일반 웹 앱

N → 배치(일괄) 앱

- 예시
    
    회원가입
    
    - 일반
    
    주에 한 번씩 전체 회원들에게 웹진(광고물)을 이메일로 발송
    
    - 배치
    
    회원가입 후 가입인사 메일 발송
    
    - 일반(가입이라는 상호 작용을 전제로 함)

배치(단위가 큰, 실행시간이 조금 더 오래)

- GET /member/join → 회원가입폼
- GET /member/sendWebzineToAllMember → 멤버들에게 웹진(광고) 발송 / TimeOut

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d3dbdcfe-1b41-4713-a2ca-43642671c816/Untitled.png)

- 제약을 해제할 수 있으나, 그만큼 부하가 많이 가는 구성이란 의미
- 제약을 해제하지 않으면서 부하도 적게 → Batch

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/301acb33-4871-4a82-b13e-a888954a89e7/Untitled.png)

- 메일 발송 서비스를 위해 회원 조회
- 100만 건의 회원을 순수 조회 → 메모리 부하

- LIMIT을 통해 제한적으로 조회 가능

```sql
SELECT *
FROM member // 100만 건이면 100만 개를 모두 스프링 메모리에 저장 -> 부하

#chunk
SELECT COUNT(*)
FROM member 

SELECT * 
FROM member
LIMIT 0, 100 // 100개씩 모든 COUNT를 돌 때까지 실행
```

## ❗️ 실습

### application.yml

```yaml
spring:
  batch:
    job:
      names: ${job.name:NONE}
    jdbc:
      initialize-schema: ALWAYS
  datasource:
    url: jdbc:mariadb://127.0.0.1:3306/app_2022_09_22?useUnicode=true&characterEncoding=utf8&autoReconnect=true&serverTimezone=Asia/Seoul
    driver-class-name:
    username: root
    password:
  jpa:
    show-sql: true
```

### HelloWorldJobConfig

```java
@Configuration
@RequiredArgsConstructor
public class HelloWorldJobConfig {
    private final JobBuilderFactory jobBuilderFactory;

    private final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job helloWorldJob() {
        return jobBuilderFactory.get("helloWorldJob")
                .incrementer(new RunIdIncrementer()) // 강제로 매번 다른 ID를 실행시에 파라미터로 부여
                .start(helloWorldStep1())
                .build();
    }

    @Bean
    @JobScope
    public Step helloWorldStep1() {
        return stepBuilderFactory.get("helloWorldStep1")
                .tasklet(helloWorldTasklet())
                .build();
    }

    @Bean
    @StepScope
    public Tasklet helloWorldTasklet() {
        return (contribution, chunkContext) -> {
            System.out.println("헬로월드!");

            return RepeatStatus.FINISHED;
        };
    }
}
```

### Application

```java
@SpringBootApplication
@EnableBatchProcessing  // spring batch를 실행하기 위한 어노테이션
public class App20220922Application {

    public static void main(String[] args) {
        SpringApplication.run(App20220922Application.class, args);
    }

}
```

# 💡 MyBatis

## ❗️ 특징

- MyBatis
    - SI, 공공기관 등에서 많이 쓰임
    - 단순하다
- JPA
    - 스타트업 등에서 많이 쓰임
    - 복잡하다


❗️ORM의 개념, JPA와 MyBatis 차이

데이터들이 프로그램이 종료되어도 사라지지 않고 어떤 곳에 저장되는 개념을 영속성(Persistence) 이라고 한다.

자바에서는 데이터의 영속성을 위한 JDBC를 지원해주는데, 이는 매핑 작업을 개발자가 일일히 수행해야 하는 번거로움이 있다.

SQL Mapper와 ORM은 개발자가 직접 JDBC Programming을 하지 않도록 기능을 제공해주는 Persistence Framework 종류이다.

기존 JDBC 만의 사용으로 쿼리문을 만들어 요청하는 과정은 쿼리문이 조금만 길어져도 관리가 힘들거 번거롭다. 따라서 JPA와 MyBatis 라이브러리를 사용하여 문제를 해결할 수 있다.

> SQL Mapper
Object와 SQL의 필드를 매핑하여 데이터를 객체화 하는 기술
객체와 테이블 간의 관계를 매핑하는 것이 아님
SQL문을 직접 작성하고 쿼리 수행 결과를 어떠한 객체에 매핑할지 바인딩 하는 방법
DBMS에 종속적인 문제
EX) JdbcTemplate, MyBatis
> ORM
Object와 DB테이블을 매핑하여 데이터를 객체화하는 기술
개발자가 반복적인 SQL을 직접 작성하지 않음
DBMS에 종속적이지 않음
복잡한 쿼리의 경우 JPQL을 사용하거나 SQL Mapper을 혼용하여 사용 가능
> JPA(ORM)
JPA : ORM(Object Relational Mapping) 기술
자바 ORM의 기술 표준
대표적인 오픈소스로 Hibernate
CRUD 메소드 기본 제공
쿼리를 만들지 않아도 됨
1차 캐싱, 쓰기지연, 변경감지, 지연로딩 제공
MyBatis는 쿼리가 수정되어 데이터 정보가 바뀌면 그에 사용 되고 있던 DTO와 함께 수정해주어야 하는 반면에, JPA 는 객체만 바꾸면 된다.
즉, 객체 중심으로 개발 가능
but 복잡한 쿼리는 해결이 어려움
> MyBatis(SQL Mapper)
MyBatis : Object Mapping 기술
자바에서 SQL Mapper를 지원해주는 프레임워크
SQL문을 이용해서 RDB에 접근, 데이터를 객체화 시켜줌
SQL을 직접 작성하여 쿼리 수행 결과를 객체와 매핑
쿼리문을 xml로 분리 가능
복잡한 쿼리문 작성 가능
데이터 캐싱 기능으로 성능 향상
but 객체와 쿼리문 모두 관리해야함, CRUD 메소드를 직접 다 구현해야함.
-정리-

> ORM 이란?
객체(Object)와 DB의 테이블을 Mapping 시켜 RDB 테이블을 객체지향적으로 사용하게 해주는 기술

객체와 RDB를 별개로 설계하고 중간에서 ORM이 매핑해주는 역할을 한다. 즉, ORM은 SQL문이 아닌 RDB의 데이터 그 자체와 매핑하기 때문에 SQL을 직접 작성하지 않는다.

여기서 RDB 테이블이란, 관계형 데이터베이스를 말한다. 참고

RDB 테이블은 객체지향적 특성(상속, 다형성, 레퍼런스) 등이 없어서 Java와 같은 객체 지향적 언어로의 접근이 쉽지 않다.

이럴때 ORM을 사용하면 보다 객체지향적으로 RDB를 사용할 수 있다!

Java에서 사용하는 대표적인 ORM으로는 JPA와 그의 구현체 Hibernate가 있다. JPA가 등장하기 전에는 위에 설명했듯이 MyBatis라는 Object Mapping 기술을 이용하였는데, MyBatis는 Java 클래스 코드와 직접 작성한 SQL 코드를 매핑 시켜주어야 했다.

하지만! JPA와 같은 ORM기술은 객체가 DB에 연결되기 때문에 SQL을 직접 작성하지 않는다.

따라서 JPA라는 ORM 기술에 의해 DB에서 조회한 데이터들이 객체로 연결되어 있고, 객체의 값을 수정하는 것은 DB의 값을 수정하는 것이라고 할 수 있다.




## 이번 주 수업을 마치며..

김동현2 : 

이승훈 : 

손정아 :  

박종수 : 밀린 진도를 따라가기 바쁘고 프로젝트의 적용하기는 더 어렵다... 일단 해보는데 까지 해보자

