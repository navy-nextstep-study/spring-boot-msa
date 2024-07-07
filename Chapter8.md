## 질문
### 근우
- Non-Repeatable Read 와 Phantom Read 가 실무에서 문제가 될 만한 상황이 뭐가 있을까(실질적으로 의미가 있는 격리 수준인지 같이 고민해보자)

### 이슬

### 창현
- 운영 환경에서의 적절한 데이터베이스 격리수준은 무엇일까?
   - Postgresql, Oracle 등의 경우 MySQL 격리 수준과 다른 Read Commited인데, Non-Repeatable Read와 같은 문제가 발생해도 괜찮을까
- 프로젝트시 OSIV를 끄는 편인지, 키는 편인지

## 실습문제
모놀리식 구조로 된 서버에서 Order(주문) 도메인과 Product(상품) 도메인이 존재한다. 클라이언트가 주문을 하면 상품의 수량이 -1씩 감소하는 구조로 설계되어있으며, 하나의 주문에는 하나의 상품만 포함된다. 해당 서버에서는 상품과 관련된 이벤트가 자주 열려 주문 로직에 포함되는 상품의 수량 감소 로직은 JPA의 PessimisticLock을 이용하여 동시성을 제어하고있다. 

개발팀은 PessimisticLock을 사용하여 처리하는 것이 과연 성능이 좋을까 고민하게 되었고, 여러가지 방법을 시도해보고자 Queue를 활용하여 구현해서 성능을 비교해보기로 했다.

[기존 코드](https://github.com/Hchanghyeon/programming-learn/tree/main/lock-pessimistic-jpa/src/main/java/com/lock/lockpessimisticjpa)를 참고, 복붙하고 BlockingQueue와 별도의 WorkerThread를 활용하여 기존 코드를 수정하여 동시성을 제어해보자.

- 구현 방법에 정답은 없으며 BlockingQueue를 활용해서 Application Level에서 동시성을 제어하는 것을 목적으로 둔다.
- BlockingQueue의 구현체는 ArrayBlockingQueue로 하며, Queue의 Capacity는 1000으로 설정한다.
- BlockingQueue에 데이터가 꽉 찼을 때 대기하도록 구현한다.
- 동시성 테스트는 Jmeter를 이용하여 한 번에 1000명으로 가정하고 실행한다.


#### Product(상품) 생성 API
주소
```
http://localhost:8080/products
```

RequestBody
```json
{
    "name" : "ball",
    "quantity" : 2000
}
```

#### Order(주문) 생성 API
주소
```
http://localhost:8080/orders
```

RequestBody
```json
{
    "itemId" : 1
}
```

**상품 생성 후 Jmeter를 활용하여 주문 생성 API 1000번 요청 후 Lock 기법과 BlockingQueue 기법의 성능 차이를 알아보자**
