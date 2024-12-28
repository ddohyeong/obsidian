---
title: Spring Security란?
categories:
  - Spring
  - Security
tags:
  - Security
toc: true
toc_sticky: true
date: 2024-05-07
posting: true
---

# Spring Security란
> 인증 , 권한부여 및 보호 기능을 제공하는 프레임워크
> Spring 기반 애플리케이션 보안을 위한 사실상의 표준

# Authentication(인증), Authorization(인가)

**Authentication(인증)**
해당 사용자가 본인이 맞는지를 확인하는 절차 
- Spring Security는 credential 기반의 인증을 취함
## credential 방식이란
- username, password를 이용하는 방식 


**Authorization(인가)**
인증된 사용자가 요청된 자원에 접근 가능한가를 결정하는 절차

> 🧑🏻‍💻 특정 권한을 얻기 위해서 유저는 인증(Authentication) 이 필요하고 관리자는 해당 정보를 참고해 권한을 인가(Authorization)한다.
   username - password 패턴의 인증방식을 거치기 때문에 **스프링 시큐리티는 principal - credential 패턴**을 가지게 된다.

**Spring Security는 Filter 기반으로 동작함**

# <mark style='background:#4b6584'>Spring Security Architecture</mark>

![](https://i.imgur.com/Bo0TWpu.png)

1. 사용자가 로그인 정보로 로그인(Authentication)을 요청
2. AuthenticationFilter가 정보를 인터셉트하여 UsernamePasswordAuthenticationToken(인증 객체임)을 생성하여 AuthenticationManager에게 인증 객체를 전달
3. AuthenticationManager인터페이스가 AuthenticationProvider에게 정보 전달, 등록된 AuthenticationProvider를 조회하여 인증을 요구
4. AuthenticationProvider는 UserDetailService를 통해 입력받은 아이디에 대한 사용자 정보를 DB에서 조회함 
	- 입력 받은 비밀번호를 암호화하여 DB의 비밀번호와 매칭시켜 일치하는 경우 인증된 UsernamePasswordAuthenticationToken을 생성하여 AuthenticationManager로 전달함 
5. AuthenticationManager는 Authentication 객체를 AuthenticationFilter 로 전달 
6. AuthenticationFilter는 전달받은 UsernameAuthenticationToken을 LoginSuccessHandler로 전송하고 SecurityContextHolder 에 저장함

 