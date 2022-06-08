# spring_beginner
[인프런] 김영한님의 강좌 **스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술** 를 통해 학습한 내용을 바탕으로
<br> 작성하였습니다.

<br>

## 1. 라이브러리

💡 Gradle은 **의존관계**가 있는 라이브러리를 함께 다운로드 한다.


ex)  🟢는 🔺을 🔺은 🟦를 필요로 한다면,  🟢🔺🟦 모두를 다운받는다.

👉 **“스프링 부트 라이브러리”**

- **spring-boot-starter-web(핵심)**
    - spring-boot-starter-tomcat: 톰캣(웹서버) → 서버로 라이브러리 연동(요즘은 임베디드 되어있음)
    - sprint-webmvc: 스프링 웹 MVC
- spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)
- spring-boot-starter(공통): **스프링 부트 + 스프링 코어 + 로깅**
    - spring-boot
        - spring-core
    - spring-boot-starter-logging
        - logback, slf4
    
    ---
    
    👉 **“테스트 라이브러리”**
    
    - spring-boot-starter-test
        - junit: 테스트 프레임워크 (5를 사용)
        - mockito: 목 라이브러리
        - assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
        - spring-test: 스프링 통합 테스트 지원


<br>


## 2. View 환경설정
### **Welcome Page 만들기**

*도메인만 누르고 들어왔을때 첫 페이지*

> `resources/static/index.html`
> 

```html
<!DOCTYPE HTML>
<html>
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>
```

- 스프링 부트가 제공하는 **Welcome Page** 기능
    - `static/index.html` 을 올려두면 Welcome Page 기능을 제공한다.
- **thymeleaf** 템플릿 엔진
    - thymeleaf 공식 사이트: [https://www.thymeleaf.org/](https://www.thymeleaf.org/)
    - 스프링 공식 튜토리얼: [https://spring.io/guides/gs/serving-web-content/](https://spring.io/guides/gs/serving-web-content/)

**“thymeleaf 템플릿엔진 동작 확인”**

- 실행: http://localhost:8080/hello

![동작환경그림](https://user-images.githubusercontent.com/87354210/172509542-da513010-281e-4922-ad2d-572d43184cb0.jpg)


- 컨트롤러에서 리턴 값으로 문자를 반환하면 **뷰 리졸버(`’viewResolver’`)** 가 화면을 찾아서 처리한다.
    - 스프링 부트 템플릿엔진 기본 viewName 매핑
    - `‘resources: templates/’` +{viewName}+`’.html’`

> 참고: `‘spring-boot-devtools’` 라이브러리를 추가하면, ‘html’ 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능하다.

<br>

## 3. 스프링 웹 개발 기초

💡 **정적 컨텐츠** <br>
💡 **MVC와 템플릿 엔진** <br>
💡 **API**

</aside>

## 정적 컨텐츠

- 스프링 부트 정적 컨텐츠 기능
- [https://docs.spring.io/spring-boot/docs/2.4.13/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content](https://docs.spring.io/spring-boot/docs/2.4.13/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)

![정적컨텐츠](https://user-images.githubusercontent.com/87354210/172509891-ca8ca620-550f-4ec7-a9fd-09b9d1be5be8.png)


- **작동원리**
    1. `hello-static.html`이 들어오면 톰캣 서버에서 확인.
    2. 스프링 부트는 Controller쪽에서 먼저 **hello-static** 관련 컨트롤러를 찾아봄.
    3. 그러나 **hello-static**이라는 컨트롤러는 없음. 따라서 스프링 부트는 `resources: static/hello-static.html`을 찾게됨.
    4. 찾으면 바로 리턴

## MVC와 템플릿 엔진

- **MVC:** Model, View, Controller
    
    **View** 는 보이는 것에만 집중
    
    **Controller**는 비지니스 로직, 서버와 같이 내부적인 컨텐츠를 처리
    
    **Model**에 View와 Controller의 내용을 담아서 처리
    
    **“Controller”**
    
    ```java
    @Controller
    public class HelloController {
    	  @GetMapping("hello-mvc")
        public String helloMvc(@RequestParam("name") String name, Model model) {
            model.addAttribute("name", name);
            return "hello-template";
        }
    }
    ```
    
    **“View”**
    
    `‘resources/template/hello-template.html’`
    
    ```html
    <html xmlns:th="http://www.thymeleaf.org">
    <body>
    <p th:text="'hello ' + ${name}">hello! empty</p>
    </body>
    </html>
    ```
    
    **“실행”**
    
    - http://localhost:8080/hello-mvc?/name=spring
    
    **“MVC, 템플릿 엔진 이미지”**
    
    ![mvc템플릿이미지](https://user-images.githubusercontent.com/87354210/172510163-d418a5a2-3ea9-4b27-b942-4cf76c750f91.png)

    
    - **작동원리**
        1. **내장 톰켓 서버에서 localhost의 내용을 전달받음.**
        2. **스프링 부트에서 helloController에 mapping되어있으므로 return해줌 .**
            
            → **이름은 hello-template로 리턴, 파라미터는 model
                (key는 name이고, attr은 spring임)**
            
        3. **스프링은 model이 담긴 viewResolver를 동작시킴.**
        4. **Thymeleaf 템플릿 엔진이 viewResolver의 model을 렌더링해서 변환 후, 웹 브라우저로 전송.**

## API

**“@ResponseBody 문자 반환”**

```java
@Controller
public class HelloController {

	@GetMapping("hello-string")
	@ResponseBody
	public String helloString(@RequestParam("name") String name) {
    return "hello " + name;     //"hello spring"
	}
}
```

- `‘@ResonseBody’` 를 사용하면 **뷰 리졸버**(`’viewResolver’`)를 사용하지 않음
- 대신에 HTTP의 BODY에 문자 내용을 직접 반환 (HTML, BODY TAG를 말하는 것이 아님)

**“실행”**

- http://localhost:8080/hello-string?name=spring

**“@ResponseBody 사용원리”**

![responsebody](https://user-images.githubusercontent.com/87354210/172510218-ca7edd9e-df03-4829-8089-a3d5be71ea8a.png)


- **작동원리**
    - `‘@ResponseBody’` 를 사용
    - HTTP의 BODY에 문자 내용을 직접 반환
    - `‘viewResolver’` 대신에 `‘HttpMessageConverter’` 가 동작
        
        (기본 문자인 경우: StringConverter가 동작
        
        객체인 경우: JsonConverter가 동작)
        
    - 기본 문자처리: `‘StringHttpMessageConverter’`
    - 기본 객체처리: `‘MappingJackson2HttpMessageConverter’`
    - byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음

> **참고: 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 `‘HttpMessageConverter’`가 선택된다.**
>


