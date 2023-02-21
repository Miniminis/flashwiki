# spring dependency injection 은 어떻게 동작할까

> 아래의 테스트가 왜 성공적인지 생각해보자. 이상함을 느낄 수 있어야 한다.&#x20;

```java
//DiService 
@Service
public class DiService {

    @Autowired
    DiRepository diRepository;

}

//DiRepository
@Repository
public class DiRepository {

}


//Test 
@SpringBootTest
class DiServiceTest {

    @Autowired DiService diService;

    @Test
    void diTest() {
        assertThat(diService).isNotNull();
        assertThat(diService.diRepository).isNotNull();
    }
}
```



> 리플렉션을 통해서 그 원리를 알아보자.&#x20;

