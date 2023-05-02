---
title: 221016 Mapper class Bean 등록 Issue
date: 2022-10-16 16:01:00
tags:
  - Spring
categories:
  - Resolved-Error 
---

<div align="center">
  <img src="/images/post_images/221016_spring_bean_container.png" alt="Spring bean container">
</div>

<br/>
<br/>

우선 본 포스팅에서 다루고자 하는 에러가 어떤 상황에서 발생하게 되었는지에 대해서 간략하게 설명을 하고, 어떤 접근으로 해결하려고 했는지에 대해서 기술해보려고 한다.

<ins><b>프로젝트 진행</b></ins>

회사에서 진행중인 프로젝트에서 백엔드를 구성하면서 간단한 CRUD 처리의 경우에는 JPA를 사용하고, 복잡한 쿼리 사용의 경우에는 MyBatis를 사용하기로 했다. 여기서 말하는 복잡한 쿼리란 간단한 CRUD 이외의 복잡한 쿼리를 말한다.
그냥 이렇게 하라고 하니깐 하는 게 아니라 이렇게 구성을 하면 어떤 이점이 있는지에 대해서 간단하게 짚고 넘어가도록 하자.

- <b>Hibernate vs MyBatis</b>

  과거 EJB2로 개발을 하던 당시 Gavin king이라는 사람이 사용자 친화적이지 않은 자바 애플리케이션 개발 방식을 좀 더 사용자 친화적이게 만들고자 개발을 하게 된 것이 바로 그 유명한 Hibernate이다. 이 Hibernate가 점차 인기가 많아지자 자바 진영에서 Gavin king을 영입해서 자바 `ORM(Object Relational Mapping)` 기술에 대한 표준 명세를 개발하도록 하였는데, 그것이 바로 `JPA(Java Persistent API)`이다.

  JPA는 ORM을 사용하기 위한 인터페이스를 모아둔 것으로, 이를 구현한 프레임워크 중 대표적으로 Hibernate가 있고, Spring에서는 대부분 Hibernate를 사용하고 있다.  
  Spring에서는 JPA를 사용할 때 구현체들을 직접 다루지 않고, `구현체들을 좀 더 쉽게 사용하고자 추상화 시킨 Spring Data JPA라는 모듈을 시용하여 JPA 기술을 다룬다.`

  그렇다면, JPA 하나만 사용해서 개발하면 만능일까? 찾아보니 대부분 단순한 CRUD의 처리를 하는 경우에는 JPA만을 사용해도 괜찮다고 한다. 하지만, 복잡한 통계나 정산관련 조회 쿼리가 포함된 경우, MyBatis로 처리하면 좀 더 개발자에게 편하다. 물론 JPA로도 복잡한 집계성 쿼리를 처리하는 것도 가능은 하지만 구현이 쉽지 않기 때문에 MyBatis를 사용하는 것이 낫다고 한다.

  따라서 JPA 또는 MyBatis만 사용하는 것이 아닌, 두 개를 적절히 조합해서 프로젝트를 진행하면 업무적으로 효율이 높아질 수 있다는 결론이 나온다.
<br/>
- <b>MyBatis의 사용</b>

  MyBatis는 Mapper를 별도로 구성하며, SqlSession을 직접 사용하는 형태가 아닌 Mapper를 통해 처리한다. 기본적으로 Mapper를 사용하게 되면, Mapping 파일이 자동으로 Mapper의 단위가 되기 때문에 유지보수 및 관리에 용이하다.

<br/>

<!-- more -->

<ins><b>문제상황</b></ins>
  
  JPA와 MyBatis를 하나의 프로젝트에서 구성을 하면서 직면한 문제는 `@Mapper` annotation을 붙여서 정의한 Mapper class의 Bean 객체가 등록이 되지 않아 Service 클래스에서 해당 Mapper class의 Bean 객체의 의존성 주입시에 아래의 에러가 발생하였다.

  `"Consider defining a bean of type '{Mapper class package detail}' in your configuration."`

<br/>

<ins><b>해결책</b></ins>

  Mapper class에 달아주는 @Mapper annotation은 기본적으로 `@Component annotation 없이 클래스를 generate해준다.` @Component annotation은 기본적으로 Spring container에 Bean 객체를 등록해주기 위해 스캔해주는 과정을 위한 annotation이다. @Service와 @Repository, @Controller의 Custom annotation 정의를 보면, 모두 @Component annotation을 상속하고 있음을 알 수 있다.

  <br/>

  - <ins><b>solution1) *.yml 파일에 mybatis에 대한 설정 추가하기</b></ins>
    ```
    mybatis:
      mapper-locations: classpath:{구체 경로 지정}/mapper/*.xml
      type-aliases-package: ...(생략)...
    ```
  <br/>

  - <ins><b>solution2) pom.xml 파일에서 dependency의 버전 간 호환성 문제</b></ins>

    mybatis-spring-boot-starter는 1.3.2, mybatis-spring을 1.3.2로 버전을 설정한다. 
    `이 부분은 기존에 작업하고 있는 백엔드 프로젝트의 pom.xml의 버전 호환을 확인하여 수정할 필요가 있다.` (`현재 별도로 백엔드 프로젝트를 작업중...`) 

    일단 문제상황 재현을 위해 생성한 프로젝트의 pom.xml에서 아래와 같이 mybatis-spring-boot-starter(1.3.2), mybatis(3.4.6), mybatis-spring(1.3.2)로 버전을 맞춰서 다시 Maven clean 및 install을 해주니 정상적으로 Mapper class의 Bean이 등록되어 에러 메시지가 더 이상 나오지 않았다.

    ```yml
    <dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.2</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.6</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.3.2</version>
		</dependency>
    ```

