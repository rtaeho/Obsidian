MVC는 Model, View, Controller를 뜻함
템플릿 엔진은 `html`을 그대로 전달하지 않고 서버에서 동적으로 변형 후 `html` 파일로 전달

**Controller**
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
**@Controller**
: 해당 자바 클래스가 Controller임을 나타냄

**@GetMapping("name")**
: `localhost:8080/name`을 통해 요청을 받았을 때 name에 해당하는 view를 반환

**@RequestParam("name") String name, Model model**
: 웹에서 받아온 String 객체를 _Model_ 에 담고, 그 _Model_ 은 View에서 렌더링할때 사용

**model.addAttribute("name", name);**
: 파라미터로 넘어온 _name_ 을 _Model_ 에 저장

**그러므로**, URL상에서 `"localhost:8080/hello-mvc?name=값"`처럼 값을 통해 파라미터 _name_ 을 넣어 요청해야 함


**View** 
`resources/templates/hello-template.html`

```html
 <html xmlns:th="http://www.thymeleaf.org">
 <body>
 <p th:text="'hello ' + ${name}">hello! empty</p>
 </body>

</html>
```

thymeleaf 템플릿 엔진을 사용해 html을 가져옴
(thymeleaf를 사용한 html 파일은 서버 없이 절대경로로 열어도  디폴트값인 "hello! empty"가 출력)
템플릿 엔진 동작시 _Model_ 의 키값이 _name_ 에서 값을 꺼내  `'hello ' + ${name}`이라는 값으로 치환

![[Pasted image 20240214155321.png]]

1. 브라우저에서 `local:8890/hello-mvc`로 요청
    
2. 톰캣 서버에서 요청을 받아 스프링 컨테이너로 전달
    
3. 컨테이너는 가장먼저 **Controller**를 살피며 `hello-mvc`에 맵핑된 **@GetMapping()** 가 있는지 확인하고 맵핑된 **helloMvc()**  메서드를 호출
    
4. 리턴 값인 `hello-template` 을 보내고, 동시에 _Model_ 에 _키(name), 값(spring)_ 의 데이터를 넣어 전달
    
5. `src/main/resources/template`에서 **View**에 해당하는 `hello-template.html`을 찾아주고 _템플릿엔진(thymeleaf)_ 을 연결 시켜주는 **viewResolver**가 동작
    
6. _템플릿엔진(thymeleaf)_ 은 `html`파일을 받아 **View** 템플릿을 변환 후 브라우저에게 전달