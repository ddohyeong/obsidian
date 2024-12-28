---
title: "Spring \bFilter 타임아웃 정리글"
categories:
  - Spring
tags: 
toc: true
toc_sticky: true
date: 2024-10-29
posting: true
---
> Spring Timeout 기능을 구현하기 위해서 Filter를 사용할 수 있었다. 
> 모든 기능에 적용이 되야 하므로 `Dispatcher Servlelt` 으로 들어오기 전 Filter를 이용하여 Time을 설정하고 처리하는 방법으로 사용하였다. 

![](https://i.imgur.com/pSJkJmF.png)


요청에 대해 5초 동안 응답하지 못하는 경우 팝업을 알려줘야 하는 요구사항에 `Filter`로 기능을 구현했다. 
따라서 `TimeFilter` 를 구현해서 `Spring Security` 단 뒤에서 동작하도록 구현할 수 있었다. 

```java
public class TimeoutFilter implements Filter {  
    private static final int TIMEOUT = 5; // 타임아웃 시간 (초)  
  
    int corePoolSize = Runtime.getRuntime().availableProcessors();  
    int maxPoolSize = corePoolSize * 2;  
    long keepAliveTime = 60L; // 60초 후에 사용하지 않는 스레드를 제거  
  
    // Spring Security 객체 전파를 위해 DelegatingSecurityContextExecutorService 로 변경  
    ExecutorService executorService = new DelegatingSecurityContextExecutorService(  
            new ThreadPoolExecutor(  
                    corePoolSize,  
                    maxPoolSize,  
                    keepAliveTime,  
                    TimeUnit.SECONDS,  
                    new LinkedBlockingQueue<>()  
            )  
    );
```

🧐 **시큐리티 인증이 되지 않는 문제가 발생**
> Spring 의 `SpringContext`가 비동기 작업으로 인해 전파되지 않는다. 이유는 스레드 간 `SecurityContex`t의 전파가 기본적으로 설정되어있지 않다. Spring Security의 `SecurityContext`는 기본적으로 <span style='color:var(--mk-color-red)'>Thread Local</span>에 저장되기 때문에 새로운 스레드에서는 기존 스레드의 `SecurityContext`를 참조할 수 없다. 
> 
> 나머지 설정은 Thread 가 계속 생성 되는것을 막기 위해 코어 * 2 만 생성되도록 설정하였다. 이 부분은 사용에 따라 설정을 해주면 될 것 같다. 


```java
	Callable<Void> task = () -> {  
    try {  
        // 작업 중에 인터럽트 신호 감지  
        while (!Thread.currentThread().isInterrupted()) {  
            // 실제 필터 체인 처리  
            chain.doFilter(request, response);  
            return null;  
        }  
    } catch (Exception e) {  
        if (Thread.currentThread().isInterrupted()) {  
            log.warn("Task interrupted: exiting filter chain");  
        } else {  
            throw e;  
        }  
    }  
    return null;  
};


Future<Void> future = executorService.submit(task);  
  
try {  
    future.get(TIMEOUT, TimeUnit.SECONDS);  
} catch (TimeoutException e) {  
    // 타임아웃 발생 시 응답 처리  
    future.cancel(true); // 작업을 중지  
  
    HttpServletResponse httpResponse = (HttpServletResponse) response;  
  
    if (isAjaxRequest((HttpServletRequest) request)) {  
        httpResponse.setStatus(HttpServletResponse.SC_SERVICE_UNAVAILABLE);  
    } else {  
        httpResponse.sendRedirect("/timeout");  
    }
```

이후 TimeFilter 클래스를 FilterConfig 로 등록해줍니다. 
``` java
@Configuration  
public class FilterConfig {  
  
    @Bean  
    public FilterRegistrationBean<TimeoutFilter> timeoutFilter() {  
        FilterRegistrationBean<TimeoutFilter> registrationBean = new FilterRegistrationBean<>();  
        registrationBean.setFilter(new TimeoutFilter());  
        registrationBean.addUrlPatterns("/*");  
        registrationBean.setOrder(Ordered.LOWEST_PRECEDENCE); // Spring Security 필터 체인 후에 실행  
        return registrationBean;  
    }  
  
}
```
모든 URL 부터 들어오는 요청을 처리할 수 있도록 했습니다. 

>🧑🏻‍💻 이후 트랜잭션 처리를 어떻게 해야할지 대해서 작성을 할 수 있도록 하겠습니다. 

