---
title: Spring Security 구현
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

**ApplicatoinConfig 를 통한 빈 등록**
- AuthenticationProvider 등록 
	- UserDetailService를 통해 입력받은 아이디에 대한 사용자 정보를 조회 
	- provider에서 사용자 정보를 DB 조회 -> memerDetailsService() 

Member -> <span style='color:#eb3b5a'>Implements</span> UserDetails 를 해야함
```java
@Bean  
public UserDetailsService memberDetailsService() {  
    return uuid -> memberRepository.findByUuid(uuid)  
          .orElseThrow(() -> new UsernameNotFoundException("User not found : {}" + uuid));  
}
```
- AuthenticationManager 등록
- PasswordEncoder()생성
---
1. `SecurityConfiguration` 클래스 생성 - Spring Security 활성화
	- @EnableWebSecurity 설정
	- AuthenticationFilter -> JwtAuthenticationFilter
	- AuthenticationProvider -> JwtTokenProvider
	- SecurityFilterChain () 생성
	- <mark style='background:#8854d0'>CorsConfigurationSource</mark>
2. `JwtTokenProvider` 클래스 생성

3. `JwtAuthenticationFilter` 클래스 생성 - OncePerRequestFilter 상속
	- JwtTokenProvider 생성
	- UserDetailService
	- doFilter() 생성

🤔 모르겠는거
- CorsConfigurationSource
	- 
- JwtTokenProvider 클래스의 내용
	- 로그인 기능 구현하기   
- JwtAuthenticationFilter의 doFilter 메서드


## 비즈니스 로직 만들기
- 회원가입
```java
public void signUp(SignUpRequestVO signUpRequestVO) {  
    UUID uuid = UUID.randomUUID();  
     
    memberRepository.save(Member.builder()  
          .name(signUpRequestVO.getName())  
          .loginId(signUpRequestVO.getLoginId())  
          .password(new BCryptPasswordEncoder().encode(signUpRequestVO.getPassword()))  
          .uuid(uuid.toString())  
          .build());  
}
```
- 로그인
```java
public LoginResponseDTO logIn(LoginRequestVO loginRequestVO) {  
    Member member = memberRepository.findByLoginId(loginRequestVO.getLoginId())  
          .orElseThrow(() -> new IllegalArgumentException("존재하지 않는 아이디입니다"));  
          
    log.info("member: {}", member.getLoginId());  
    
    // UUID와 패스워드를 이용하여 인증을 수행  
    authenticationManager.authenticate(  
          new UsernamePasswordAuthenticationToken(  
                member.getUuid(),  
                loginRequestVO.getPassword()  
          )  
    );  
    
    return LoginResponseDTO.builder()  
          .accessToken(jwtTokenProvider.generateToken(member))  
          .build();  
}
```

---

1. JwtTokenProvider
2. JwtAuthenticationFilter
	- OncePerRequestFilter
3. SecurityConfiguration
4. UserDetails 구현하는 Member 엔티티, MemberRepository생성
5. ApplicationConfig 생성
6. member, memberRepo 생성
7. 필요 어노테이션 추가( @Configuration, @Service, @Component,,,)



