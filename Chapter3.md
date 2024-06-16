## 질문
### 근우
- 커스텀 애너테이션을 적극적으로 활용하여 개발한 사례가 있는지

### 이슬
- 

### 창현
- @Bean과 @Component를 사용하는 자기만의 특별한 기준이 있는지?
- applicationContext.getBean()으로 가져온다는 것은 의존성 주입이라고 볼 수 있는 것인지? page.162-163

## 실습문제
```java
@Configuration
public class AppConfig {

	@Bean
	public MemberRepository memberRepository() {
	    return new MemoryMemberRepository();
	}
	
	@Bean
	public MemberService memberService(){
	    return new MemberServiceImpl(memberRepository());
	}
	
	@Bean
	public OrderService orderService(){
	    return new OrderServiceImpl(memberRepository());
	}
}
```
위와 같이 memberRepository()를 여러 개의 Bean에서 호출하고 있을 때 싱글톤을 보장할 수 있을까? 싱글톤을 보장한다면 어떻게 보장하는지, 보장하지 못한다면 왜 보장하지 못하는건지에 대해 고민해보고 이야기해보자.
