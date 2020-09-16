---
title: 스프링 프레임워크 시작하기
date: 2020-09-08
categories:
- 스프링 프레임워크 가이드
tags:
- 스프링 프레임워크
- 스프링 튜토리얼
---

## Overview
본 글은 스프링 프레임워크 Version `5.2.8.RELEASE` 문서를 기반으로 작성하였습니다.

잠만보와 함께하는 스프링 프레임워크 기반의 웹 애플리케이션 개발 가이드는 스프링 프레임워크에 대하여 얕은 지식을 가지고 있는 잠만보가 스프링 프레임워크를 기반으로 웹 애플리케이션을 만들기 위한 공부 과정임을 알려드립니다.

이 가이드를 통해 만드는 웹 애플리케이션의 목표는 다음과 같습니다.

- 애플리케이션 컨텍스트 개념 잡기
- 웹 애플리케이션 컨텍스트 구성
- 임베디드 톰캣으로 웹 애플리케이션 실행
- 스프링 테스트 모듈을 통한 애플리케이션 기능 테스트
- 웹 관련 기능 확장
- 구글 SMTP 서버를 활용한 이메일 발송
- 반복적인 작업을 수행하는 스케줄링 적용
- 스프링 JDBC를 활용한 데이터베이스 액세스
- 스프링 HTTP 요청 클라이언트 활용
- 스프링 세션을 활용한 사용자 세션 관리 적용
- 스프링 시큐리티를 활용한 웹 보안 적용

## Getting Started
`스프링 프레임워크` 기반의 웹 애플리케이션은 [`start.spring.io`](https://start.spring.io/)를 통해 스프링 부트 프로젝트를 통해 쉽게 만들어볼 수 있습니다. `스프링 부트 프로젝트`는 스프링 프레임워크 기반의 애플리케이션을 좀 더 쉽게 만들기 위한 하나의 프로젝트로써 기존의 스프링 프레임워크 코어 모듈을 사용하는 `스프링 애플리케이션`입니다.

스프링 부트 프로젝트로 쉽게 시작할 수는 있지만 우리의 목표는 스프링 프레임워크에서 사용하는 개념에 대하여 알아가면서 스프링 프레임워크 기반의 웹 애플리케이션을 만들어보는 것 입니다. 스프링 프레임워크에 대해서 알아야만 여러분들이 애플리케이션 개발하는데 어려움을 느끼는 부분을 그나마 쉽게 해결할 수 있습니다. 

사실 상 스프링 프레임워크를 기반으로 웹 애플리케이션을 개발할 때 설정해야하는 부분이 생각보다 많습니다. 이 부분은 대부분의 스프링 애플리케이션 개발자들이 느끼는 바이며 이로 인해 스프링 부트 프로젝트가 진행되었다고 볼 수 있습니다. 따라서, 여러분들은 스프링 프레임워크를 어느 정도 알게되면 스프링 부트 프로젝트를 기반으로 웹 애플리케이션을 개발하시기를 추천합니다.

이제 우리는 스프링 프레임워크 기반의 애플리케이션에 대한 설정을 진행하기 위해 스프링 프레임워크에서 사용되는 개념에 대해 알아봅니다. 이 가이드를 통해 스프링 프레임워크에서 사용되는 전체적인 개념을 알아볼 순 없지만 스프링 프레임워크 기반의 웹 애플리케이션을 개발하는데 어려움이 없을 정도로만 개념을 잡으려고 합니다.

> 스프링 프레임워크에 대한 전체적인 부분은 공식 레퍼런스 또는 잘 정리된 스프링 서적을 참고하시기 바랍니다.
> https://docs.spring.io/spring/docs/5.2.8.RELEASE/spring-framework-reference/

## IoC Container
스프링 프레임워크 기반의 애플리케이션은 `제어 역전(Inversion of Control)`이라는 개념을 통해 애플리케이션이 동작합니다. 스프링 프레임워크는 `IoC 컨테이너`라는 특수한 컨테이너에 애플리케이션에서 사용되는 클래스를 담아두고 애플리케이션 개발자가 필요할 때 IoC 컨테이너에서 가져와서 사용할 수 있도록 제공합니다.

결국 스프링 프레임워크에서 사용하는 제어 역전이라는 개념은 애플리케이션에서 사용되는 클래스들에 대한 `생애 주기` 제어를 애플리케이션 개발자가 아닌 IoC 컨테이너가 담당하도록 하는 것이라고 이해할 수 있습니다. 이 특수한 컨테이너에서 관리되는 클래스들을 `빈(Bean)`, 그리고 이 빈 클래스들을 관리하고 제어를 담당하는 컨테이너를 IoC 컨테이너 또는 `빈 팩토리(Bean Factory)`라고 말합니다.

> `org.springframework.beans`와 `org.springframework.context` 패키지에는 스프링 프레임워크 IoC 컨테이너의 기초가 되는 `BeanFactory`라는 인터페이스를 제공합니다.

빈 팩토리는 애플리케이션 개발자가 사용하고자 하는 클래스들의 생애 주기를 담당하며 서로 다른 빈 클래스들이 연결하고 있는 의존성을 확인하고 이 의존성을 주입하는 것도 담당합니다. 

> `의존성 주입(Dependency Injection)`이라함은 빈 팩토리가 관리하는 `빈 클래스의 생성자` 또는 `팩토리 메소드의 매개변수` 그리고 인스턴스가 생성된 이후에 `프로퍼티`로써 설정되는 것을 말합니다.

### ApplicationContext
스프링 프레임워크는 `BeanFactory` 인터페이스를 확장하여 다양한 기능을 추가적으로 지원하는 [`ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.8.RELEASE/javadoc-api/org/springframework/context/ApplicationContext.html) 인터페이스를 제공합니다. 따라서, 스프링 프레임워크 기반의 애플리케이션을 개발할 때는 애플리케이션 컨텍스트를 주로 사용하게 됩니다. 또한, 웹 애플리케이션과 같은 특정 애플리케이션 레이어에서 사용할 수 있도록 애플리케이션 컨텍스트의 서브 인터페이스도 제공합니다. 예를 들어, `WebApplicationContext` 인터페이스는 `getServletContext()`함수를 제공하여 웹 애플리케이션에서 사용하는 `ServletContext`를 가져올 수 있도록 지원합니다.

스프링 프레임워크에서 제공하는 애플리케이션 컨텍스트는 다음과 같이 동작하게 됩니다.  
![](https://docs.spring.io/spring/docs/current/spring-framework-reference/images/container-magic.png)

애플리케이션에서 요구하는 `구성 메타정보`와 함께 애플리케이션 컨텍스트가 관리해야하는 빈 클래스들을 제공하면 애플리케이션 컨텍스트와 함께 완전히 구성된 애플리케이션이 준비됩니다. 이때, 애플리케이션 컨텍스트가 참조하는 구성 메타정보는 전통적으로 간단하고 직관적인 XML 형식을 사용하여 기술하였습니다. 그러나 스프링 3.0부터는 자바 코드를 기반으로 구성 메타정보를 기술할 수 있도록 제공하였습니다.

> 저는 XML 보다는 자바 코드 기반으로 구성 메타정보를 기술하는 것을 선호하는 편입니다. 자바 코드를 기반으로 작성할 경우 컴파일 단계에서부터 오류를 파악할 수 있다는 장점이 있습니다.

### Java-based Container Configuration
애플리케이션에서 사용하는 라이브러리들을 관리하기 위한 의존성 관리도구로 `Gradle`을 사용하기 위하여 그레이들 프로젝트로 시작합니다. 인텔리제이와 [STS](https://spring.io/tools) 같은 IDE로 쉽게 그레이들 프로젝트를 만들 수 있습니다.

먼저, 스프링 프레임워크의 애플리케이션 컨텍스트를 구성하기 위하여 build.gradle에 [`spring-context`](https://mvnrepository.com/artifact/org.springframework/spring-context/5.2.8.RELEASE) 의존성을 추가합니다.
```groovy build.gradle
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework:spring-context:5.2.8.RELEASE'
    testCompile group: 'junit', name: 'junit', version: '4.12'
}
```

스프링 `spring-context` 모듈에는 ApplicationContext 인터페이스와 함께 `AnnotationConfigApplicationContext`라는 구현체를 함께 제공합니다. 이 구현체는 자바 어노테이션 기반으로 구성 메타정보를 작성할 수 있도록 지원하는 애플리케이션 컨텍스트입니다. 

AnnotationConfigApplicationContext는 `@Configuration`과 `@Component` 그리고 `@Bean` 어노테이션을 사용하여 애플리케이션 컨텍스트를 구성합니다.

자바 기반의 구성 메타정보는 `@Configuration`을 선언하여 구성 메타정보 클래스로 기술합니다.
```java
@Configuration
public class AppConfig {}
```

그리고 다음과 같이 BasicService, SpringApplication을 생성합니다. 그리고 앞서 만들었던 AppConfig 구성 메타정보 클래스를 애플리케이션 컨텍스트를 생성하면서 구성 메타정보로 활용하도록 제공합니다.

```java
public class BasicService {}
    
public class SpringApplication {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        BasicService basicService = applicationContext.getBean(BasicService.class);
        System.out.println(basicService);
    }
}
```

SpringApplication을 실행해보면 애플리케이션 컨텍스트의 구성 메타정보 클래스에는 어떠한 빈 클래스도 등록하지 않았으므로 다음과 같이 오류가 발생할 것입니다.
```java
Connected to the target VM, address: '127.0.0.1:14173', transport: 'socket'
Exception in thread "main" org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'service.BasicService' available
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:352)
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:343)
    at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1127)
    at SpringApplication.main(SpringApplication.java:8)
Disconnected from the target VM, address: '127.0.0.1:14173', transport: 'socket'
```

이제 구성 메타정보 클래스에 빈을 등록해보도록 하겠습니다. @Configuration가 선언된 구성 메타정보 클래스에 빈 클래스를 등록할 때에는 `@Bean`을 빈 클래스를 반환하는 함수에 선언합니다.
```java
@Configuration
public class AppConfig {
    @Bean
    public BasicService basicService() {
        return new BasicService();
    }
}
```

다시 SpringApplication을 실행하면 다음과 같이 애플리케이션 컨텍스트로부터 BasicService 유형의 빈 클래스를 가져올 수 있습니다.
```java
service.BasicService@23c30a20
```

여기서 구성 메타정보 클래스에 빈 클래스를 등록하는 또 다른 방법을 소개하겠습니다. 바로 `@ComponentScan`과 `@Component` 어노테이션을 활용하는 것입니다.

### @CompnentScan
@Configuration를 선언한 클래스에 `@CompnentScan`을 선언하여 특정 `패키지 단위`로 빈을 등록할 수 있습니다. @CompnentScan는 지정된 패키지에 속한 클래스에 `@Component`가 선언되는 경우 현재 구성 메타정보 클래스에 빈으로 포함시키는 특별한 일을 수행합니다.

만약, @Component가 선언된 클래스가 특정 패키지에 있다면 다음과 같이 구성 메타정보 클래스에 직접 빈을 등록하지 않아도 포함됩니다.
```java
@ComponentScan(basePackages = {"service"})
@Configuration
public class AppConfig {}
```

그리고 service 패키지에 `ConversionService`라는 클래스를 만들고 @Component를 선언합니다. 그리고 애플리케이션 컨텍스트로부터 ConversionService 유형의 빈 클래스를 가져옵니다.

```java
@Component
public class ConversionService {}
    
public class SpringApplication {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        ConversionService conversionService = applicationContext.getBean(ConversionService.class);
        System.out.println(conversionService);
    }
}
```

다음과 같이 정상적으로 ConversionService 유형의 빈 클래스를 가져왔음을 확인합니다.
```java
service.ConversionService@1c5920df
```

그레이들을 통해 스프링 의존성을 추가하고 자바 기반의 컨테이너를 구성해보았습니다. 여기까지 내용을 정리해보면 다음과 같습니다.

- 애플리케이션 컨텍스트에서 활용하는 구성 메타정보는 `@Configuration`을 선언하여 만든다.
- @Configuration을 선언한 구성 메타정보 클래스에서 `@Bean`을 선언하여 빈을 등록할 수 있다.
- `@CompnentScan`을 선언하여` @Component`가 선언된 클래스를 패키지 단위로 빈으로 등록할 수도 있다.

이제 우리는 애플리케이션 컨텍스트를 구성하는 것, 구성 메타정보 클래스를 기술하는 것, 빈 클래스를 등록하는 법에 대하여 알게되었습니다. 

## Additional Capabilities
애플리케이션 컨텍스트는 빈 클래스들을 관리할 뿐만 아니라 추가적인 기능을 제공합니다. 애플리케이션 컨텍스트가 제공하는 추가 기능을 하나씩 다뤄보도록 하겠습니다.

### Environment Abstraction
애플리케이션 컨텍스트가 제공하는 첫번째 기능은 환경변수와 같은 프로퍼티를 관리하거나 환경을 구분할 수 있는 프로파일을 지정하는 것입니다. 

#### @Profile
@Configuration을 선언한 구성 메타정보 클래스에 [`@Profile`](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-definition-profiles-java)을 선언하여 애플리케이션 환경을 구분할 수 있습니다. @Profile이 선언된 구성 메타정보에 기술된 빈은 해당 프로파일이 활성화 되어있을 경우 등록됩니다.

앞서 만들었던 AppConfig 클래스에 @Profile을 선언하고 실행합니다.

```java
@Profile({"mambo"})
@ComponentScan(basePackages = {"service"})
@Configuration
public class AppConfig {}
```

프로파일을 지정하면 다음과 같이 애플리케이션 컨텍스트로부터 EventService 빈을 가져올 수 없습니다.
```java
Exception in thread "main" org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'service.EventService' available
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:352)
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:343)
    at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1127)
    at SpringApplication.main(SpringApplication.java:9)
```

애플리케이션 컨텍스트가 참조하는 Environment에 프로파일을 지정할 수 있도록 약간 수정합니다.

```java
public class SpringApplication {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        ConfigurableEnvironment environment = applicationContext.getEnvironment();
        environment.setActiveProfiles("mambo");
        applicationContext.register(AppConfig.class);
        applicationContext.refresh();
        EventService eventService = applicationContext.getBean(EventService.class);
        System.out.println(eventService);
    }
}

```

mambo라는 프로파일을 지정하였기에 다음과 같이 EventService 빈을 가져올 수 있게 됩니다.
```java
service.EventService@4ef74c3
```

#### @PropertySource
Environment 인터페이스를 통해 애플리케이션에서 사용하는 환경변수인 프로퍼티를 관리할 수 있습니다. [`@PropertySource`](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-using-propertysource)는 구성 메타정보 클래스에 선언되어 `.properties` 파일에 기술된 프로퍼티 정보를 Environment에 주입할 수 있도록 지원합니다.

다음과 같이 클래스패스에 application.properties 파일을 생성하여 프로퍼티를 기술합니다.
```properties application.properties
spring.application.name=SpringApplication
```

그리고 @PropertySource를 선언하여 application.properties 파일을 지정하고 Environment를 통해 프로퍼티를 가져와봅니다.
```java
@PropertySource({"classpath:application.properties"})
@Profile({"mambo"})
@ComponentScan(basePackages = {"service"})
@Configuration
public class AppConfig {}
    
public class SpringApplication {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        ConfigurableEnvironment environment = applicationContext.getEnvironment();
        environment.setActiveProfiles("mambo");
        applicationContext.register(AppConfig.class);
        applicationContext.refresh();
        String property = environment.getProperty("spring.application.name");
        System.out.println("property: "+ property);
    }
}
```

다음과 같이 프로퍼티 파일에 기술한 프로퍼티를 가져올 수 있게 되었습니다.
```java
property: SpringApplication
```

### Internationalization
애플리케이션 컨텍스트가 제공하는 또 다른 기능은 메시지 소스를 관리하는 것입니다. `MessageSource` 인터페이스는 `i8n`이라고 하는 국제화 기능을 지원합니다. 애플리케이션 컨텍스트가 실행될 때 구성 메타정보에 선언된 `MessageSource` 유형의 빈을 자동으로 찾아 메시지 소스로 등록하게 되어있습니다.

스프링 프레임워크는 `ResourceBundleMessageSource`와 같은 MessageSource 구현체를 제공합니다. 대부분의 스프링 프레임워크 기반의 애플리케이션은 ResourceBundleMessageSource를 메시지 소스로 등록하여 사용하며 이 구현체에 대한 대안으로 `핫 리로드`를 지원하는 `ReloadableResourceBundleMessageSource`도 있습니다.

메시지 소스로 관리할 메시지가 포함되어있는 messages.properties 파일을 클래스패스에 추가합니다.
```properties messages.properties
argument.required=The {0} argument is required.
```

구성 메타정보 클래스에 ResourceBundleMessageSource를 빈으로 등록합니다. 이때, [setBasenames](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/support/AbstractResourceBasedMessageSource.html#setBasenames-java.lang.String...-) 함수를 사용하여 클래스패스에 있는 messages*.properties를 찾아 메시지 소스로 등록하도록 합니다. 

ResourceBundleMessageSource는 setBasenames에 지정된 이름으로 시작하는 리소스들을 불러와서 메시지로 저장합니다. 예를 들어, `messages.properties`, `messages_ko.properties`, `messages_en.properties`와 같이 언어별로 메시지를 구분할 수 있습니다.

```java
@PropertySource({"classpath:application.properties"})
@Profile({"mambo"})
@ComponentScan(basePackages = {"service"})
@Configuration
public class AppConfig {
    @Bean
    public MessageSource messageSource() {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        messageSource.setBasenames("messages");
        return messageSource;
    }
}
```

그리고 애플리케이션 컨텍스트에서 메시지 소스에 등록된 메시지를 가져올 수 있습니다.

```java
public class SpringApplication {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        ConfigurableEnvironment environment = applicationContext.getEnvironment();
        environment.setActiveProfiles("mambo");
        applicationContext.register(AppConfig.class);
        applicationContext.refresh();
        String message = applicationContext.getMessage("argument.required", new Object[]{"username"}, Locale.getDefault());
        System.out.println(message);
    }
}
```

다음과 같이 메시지의 {0}부분이 매개변수 "username"로 치환하여 제공하였습니다.
```java
The username argument is required.
```

### Access Resources 
애플리케이션 컨텍스트는 `ResourceLoader` 인터페이스를 통하여 `Resource` 오브젝트를 불러올 수도 있습니다. Resource는 클래스패스, 파일 시스템, 표준 URL등 다양한 방식으로 로우-레벨의 리소스를 가져올 수 있습니다.

클래스패스에 README.md 파일을 만들고 애플리케이션 컨텍스트로부터 파일을 가져와보겠습니다.
```java
public class SpringApplication {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        ConfigurableEnvironment environment = applicationContext.getEnvironment();
        environment.setActiveProfiles("mambo");
        applicationContext.register(AppConfig.class);
        applicationContext.refresh();
        
        try {
            Resource resource = applicationContext.getResource("classpath:README.md");
            String readme = StreamUtils.copyToString(resource.getInputStream(), StandardCharsets.UTF_8);
            System.out.println(readme);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

다음과 같이 `README.md` 파일 내용이 출력되었습니다. 
```java
An application context is a ResourceLoader, which can be used to load Resource objects.
```

### Event Publication
애플리케이션 컨텍스트는 `ApplicationEventPublisher` 인터페이스를 통하여 컨테이너에 존재하는 빈들에게 이벤트를 발행할 수 있습니다. 그리고 빈 클래스에서 `@EventListener`를 선언한 메소드에서 애플리케이션 컨텍스트가 발행한 이벤트를 수신하여 처리할 수 있습니다.

```java
public class SpringApplication {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        ConfigurableEnvironment environment = applicationContext.getEnvironment();
        environment.setActiveProfiles("mambo");
        applicationContext.register(AppConfig.class);
        applicationContext.refresh();
        applicationContext.publishEvent("Published!");
    }
}
```

애플리케이션 컨텍스트를 통해 이벤트를 발행하였습니다. 이제 해당 이벤트를 수신하기 위하여 @EventListener를 선언한 메소드를 만듭니다.

```java
@Component
public class EventService {
    @EventListener
    public void handle(String message) {
        System.out.println(message);
        // Published!
    }
}
```

앞선 예제 코드에서는 String 유형의 메시지를 발행하였으나 특정 빈 클래스들이 이벤트를 수신하여 처리할 수 있도록 이벤트 오브젝트를 만들어 발행할 수 있습니다. 예를 들어, 애플리케이션 컨텍스트의 `start()` 함수를 호출하면 `ContextStartedEvent` 이벤트가 발행됩니다.

```java
applicationContext.start();

@EventListener
public void startUp(ContextStartedEvent startedEvent) {
    System.out.println("Context started : " + startedEvent.getSource());
    // Context started : org.springframework.context.annotation.AnnotationConfigApplicationContext@51cdd8a, started on Thu Sep 10 23:48:00 KST 2020
}
```

## Aware
Aware 인터페이스는 빈 클래스가 사용되는 시점에 Setter 기반의 의존성을 주입할 수 있도록 제공합니다. 스프링 프레임워크는 기본적으로 제공하는 *Aware 인터페이스가 있습니다. 스프링 프레임워크에 포함된 대부분의 클래스들도 이 Aware 인터페이스를 포함하고있습니다.

- [`ApplicationContextAware`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContextAware.html)
- [`EnvironmentAware`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/EnvironmentAware.html)
- [`ResourceLoaderAware`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ResourceLoaderAware.html)
- [`MessageSourceAware`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/MessageSourceAware.html)
- [`ServletContextAware`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/ServletContextAware.html)
- [`SchedulerContextAware`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/quartz/SchedulerContextAware.html)

ServletContextAware와 SchedulerContextAware와 같은 인터페이스는 특정한 애플리케이션 컨텍스트 또는 특정 인터페이스 구현체에서만 의존성을 주입할 수 있습니다.

---

스프링 프레임워크 기반의 애플리케이션을 만들기 위해서 IoC 컨테이너인 애플리케이션 컨텍스트를 구성하고 애플리케이션 컨텍스트가 제공하는 여러가지 기능에 대하여 알아보았습니다. 스프링 프레임워크는 웹 애플리케이션을 개발할 수 있도록 `WebApplicationContext`라는 확장된 애플리케이션 컨텍스트를 제공합니다. 

다음 글에서는 웹 애플리케이션 컨텍스트를 활용하여 [스프링 프레임워크 기반의 웹 애플리케이션](../building-web-application-with-spring-framework)을 만들어보는 시간을 가지겠습니다. 