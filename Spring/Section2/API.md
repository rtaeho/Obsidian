스프링 개발 시 API는 JSON형식으로 바꾸어 변환하는 것을 말함
(View 없이 HTTP BODY에 전달)

**@ResponseBody 문자 반환**
```java
 @Controller public class HelloController {

     @GetMapping("hello-string")
     @ResponseBody
     public String helloString(@RequestParam("name") String name) {
         return "hello " + name;
     }
}
```

**@ResponseBody**
: **viewResolver** 를 사용하지 않고 HTTP의 BODY에 문자 내용을 **직접** 반환 (문자열 그대로)


**@ResponseBody 객체 반환** 
```java

 @Controller public class HelloController {

     @GetMapping("hello-api")
     @ResponseBody
     public Hello helloApi(@RequestParam("name") String name) {

         Hello hello = new Hello();
         hello.setName(name);
         return hello;
     }     
     static class Hello {
     
         private String name;
         
         public String getName() {
             return name;
		}
         public void setName(String name) {
             this.name = name;
		} 
	}

}
```

**@ResponseBody** 를 사용하고, 객체를 반환하면 객체가 JSON으로 변환됨


![[Pasted image 20240214161338.png]]

**@ResponseBody** 를 사용  
HTTP의 BODY에 문자 내용을 직접 반환  
**viewResolver** 대신에 **HttpMessageConverter** 가 동작  
기본 문자처리: **StringHttpMessageConverter**
기본 객체처리: **MappingJackson2HttpMessageConverter**
byte 처리 등등 기타 여러 **HttpMessageConverter**가 기본으로 등록되어 있음


 _참고 :_ **JSON**
- Java Script Object Notation의 약자
- key-value 페어로 이루어진 데이터 오브젝트를 전달하기 위해 사용하는 개방형 표준 포맷
- http 통신 시, 데이터를 주고 받을 때 사용하는 데이터 포맷 중 하나
- null, numer, string, array, object, boolean을 사용할 수 있음