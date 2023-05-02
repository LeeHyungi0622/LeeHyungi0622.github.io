---
title: 221015 Spring boot 스터디 (작성중...)
date: 2022-10-15 11:49:00
tags:
  - Spring
categories:
  - Spring
hidden: true
secret: true
---

<div align="center">
  <img src="/images/post_images/221015_spring_logo.jpeg" alt="Spring boot">
</div>

<br/>
<br/>

이번 포스팅에서는 Spring boot의 프로젝트 구성에 대해서 작성해보려고 한다.
현재 회사에서 진행하고 있는 프로젝트의 백엔드가 Spring으로 구성이 되어있고, 각 회사마다 같은 기술스택이라도 그 구성이 다르게 적용되어있기 때문에 좀 더 빠르게 익숙해지고자 본 포스팅에 내용을 정리해서 작성하려고 한다.

## <ins><b>Custom annotation - Datasource switching</b></ins>

현재 백엔드에서는 DataMart와 DataWarehouse에 해당되는 데이터베이스와 연결이 되어있다.
스프링에서는 Type을 기반으로 Bean component를 scan하기 때문에 같은 DataSource 타입을 기반으로 하는 두 Bean 객체를 주입할 때는 구분을 해줘야 한다. (`@Primary, @`)
따라서 현재 프로젝트에서는 별도의 config package 내에 데이터베이스별로 구분을 해서 custom annotation을 만들어서 Mapper class에 적용해서 Mapper class에 해당되는 Spring Bean이 등록되어 관리되도록 하고 있다.

## <ins><b>JPA와 MyBatis의 사용</b></ins>

Spring 백엔드 프로젝트의 구성은 우선 JPA와 MyBatis를 사용하여 구성한다. 

- JPA : 간단한 CRUD 처리를 위한 query 관리
- MyBatis : 상대적으로 복잡한 query (`inc. JOIN`)
