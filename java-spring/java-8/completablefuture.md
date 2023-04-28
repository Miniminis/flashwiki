# CompletableFuture

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. 자바에서의 concurrent&#x20;

### 1-1. concorrent 소프트웨어&#x20;

* 동시에 여러 작업을 할 수 있는 소프트웨어&#x20;
* 예) 웹 브라우저로 유튜브를 보면서 키보드로 문서에 타이핑&#x20;
* 예) 녹화를 하면서 인텔리 J로 코딩을 하고, 워드에 적어둔 문설르 보거나 수정&#x20;

### 1-2. 자바에서 제공하는 concurrent programming&#x20;

* 멀티 프로세싱 (ProcessBuilder)
* 멀티 쓰레드&#x20;

### 1-3. java 의 멀티 쓰레드 프로그래밍&#x20;

* 쓰레드 간에는 순서를 보장할 수 없다.&#x20;
* Runnable&#x20;
* Thread
  * 쓰레드의 주요기능
    * sleep : 현재 쓰레드 멈춰두기. 다른 쓰레드가 처리할 수 있도록 기회를 주지만, 그렇다고 락을 놔주지는 않는다. 잘못하면 deadlock! &#x20;
    * interrupt : 다른 쓰레드 깨우기. 다른 쓰레드를 깨워서 interruptedException 을 발생시킨다. 그 에러가 발생했을 때 할 일은 구현하기 나름이다. 종료시키거나 하던 일을 계속할 수 있음&#x20;
    * join : 다른 쓰레드 기다리기. 다른 쓰레드가 끝날때까지 기다린다.&#x20;

```java
public class MultiThreadJava {

    @Test
    void Thread를_상속받아_구현하는_방법() {
        HelloThread helloThread = new HelloThread();
        helloThread.start();

        System.out.println("Current Thread : " + Thread.currentThread().getName());
        //New Thread : Thread-0
        //Current Thread : main

        //Current Thread : main
        //New Thread : Thread-0
    }

    static class HelloThread extends Thread {
        @Override
        public void run() {
            System.out.println("New Thread : " + Thread.currentThread().getName());
        }
    }

    @Test
    void Runnable로_구현하거나_람다를_쓰는_방법() {
        Thread thread = new Thread(() -> System.out.println("New Thread" + Thread.currentThread().getName()));
        thread.start();

        System.out.println("Current Thread : " + Thread.currentThread().getName());
        //New ThreadThread-0
        //Current Thread : main

        //Current Thread : main
        //New ThreadThread-0
    }

    @Test
    void 쓰레드_깨우기() throws InterruptedException {
        Thread thread = new Thread(() -> {
            while (true) {
                System.out.println("New Thread" + Thread.currentThread().getName());
                try {
                    Thread.sleep(1000L);
                } catch (InterruptedException e) {
                    System.out.println("wake!");
                    return;
                }
            }
        });
        thread.start();

        System.out.println("Current Thread : " + Thread.currentThread().getName());
        Thread.sleep(3000L);
        thread.interrupt();
    }

    @Test
    void 쓰레드_기다리기() throws InterruptedException {
        Thread thread = new Thread(() -> {
            System.out.println("New Thread" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                System.out.println("wake!");
                return;
            }
        });
        thread.start();

        System.out.println("Current Thread : " + Thread.currentThread().getName());
        thread.join();
        System.out.println("thread finished! = " + thread.getName());

        //New ThreadThread-0
        //Current Thread : main
        //thread finished! = Thread-0
    }
}
```

### 1-4. 참고&#x20;

* [https://docs.oracle.com/javase/tutorial/essential/concurrency/](https://docs.oracle.com/javase/tutorial/essential/concurrency/)&#x20;
* [https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#interrupt--](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#interrupt--)

### 1-5. 문제점&#x20;

* thread 가 2개만 되어도 코드 상으로 프로그래머가 관리하기가 매우 까다로워진다.&#x20;
* 그래서 등장한 것이 Executors

## 2. Executors&#x20;

### 2-1. 고수준(high-level) concurrency programming&#x20;

* 쓰레드를 만들고, 관리하는 작업을 어플리케이션에서 분리한다.&#x20;
* 그런 기능을 Executors 에게 위임한다.&#x20;

### 2-2. Executors 가 하는 일&#x20;

1. 쓰레드 만들기 : 어플리케이션이 사용할 쓰레드 풀을 만들어서 관리한다.&#x20;
2. 쓰레드 관리 : 쓰레드 생명 주기를 관리한다.&#x20;
3. 작업처리 및 실행 : 쓰레드로 실행할 작업을 제공할 수 있는 API 를 제공한다.&#x20;

### 2-3. 주요 인터페이스&#x20;

* Executor : execute(Runnable)&#x20;
* ExecutorService : Executor 를 상속받은 인터페이스
  * Callable 도 실행할 수 있다.&#x20;
  * Executor 를 종료시키거나&#x20;
  * 여러 Callable 을 동시에 실행하는 등의 기능을 제공한다.&#x20;
* ScheduledExecutorService : ExecutorService 를 상속받은 인터페이스로 특정 시간 이후에 혹은 주기적으로 작업을 실행할 수 있다.&#x20;

<figure><img src="../../.gitbook/assets/image (14) (3).png" alt=""><figcaption></figcaption></figure>

* ExecutorService 내부를 보면, 쓰레드 풀이 있고, Blocking Queue가 존재한다.&#x20;
  * 따라서 여러개의 쓰레드 풀로 빠르게 처리할 수 없을 때에는 blocking queue에 테스크를 쌓아두고 순차적으로 처리하게 된다.&#x20;
  * 그래서 5개의 작업을 보내도, 두 개의 쓰레드로 순차적으로 처리할 수 있음
  * submit -> blocking queue -> thread pool&#x20;

```java

public class ExecutorsTest {

    @Test
    void 싱글_쓰레드_만들기() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        executorService.submit(runHello("Java"));
        executorService.submit(runHello("Spring"));
        executorService.submit(runHello("JPA"));
        executorService.submit(runHello("Http"));
        executorService.submit(runHello("MySQL"));
        //Java : pool-1-thread-1
        //Spring : pool-1-thread-1
        //JPA : pool-1-thread-1
        //Http : pool-1-thread-1
        //MySQL : pool-1-thread-1
    }

    @Test
    void 쓰레드_종료() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        executorService.submit(() -> System.out.println("Hello : " + Thread.currentThread().getName()));

        executorService.shutdown(); //graceful shutdown : thread 내부의 일을 모두 처리하고 꺼짐
        executorService.shutdownNow();  //thread 실행 중 언제라도 꺼질 수 있음
    }

    @Test
    void 쓰레드풀_만들기() {
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        executorService.submit(runHello("Java"));
        executorService.submit(runHello("Spring"));
        executorService.submit(runHello("JPA"));
        executorService.submit(runHello("Http"));
        executorService.submit(runHello("MySQL"));
        //Java : pool-1-thread-1
        //Spring : pool-1-thread-2      //2번 쓰레드에서 실행됨!
        //JPA : pool-1-thread-1
        //Http : pool-1-thread-1
        //MySQL : pool-1-thread-1
    }

    private Runnable runHello(String message) {
        return () -> System.out.println(message + " : " + Thread.currentThread().getName());
    }
}
```

### 2-4. Fork/Join framework&#x20;

* ExecutorService 의 구현체로 손쉽게 멀티 프로세서를 활용할 수 있게끔 도와준다.&#x20;
* Java 7 부터 추가됨&#x20;

### 2-5. 참고&#x20;

* [https://docs.oracle.com/javase/tutorial/essential/concurrency/executors.html](https://docs.oracle.com/javase/tutorial/essential/concurrency/executors.html)

## 3. Callable and Future&#x20;

### 3-1. Callable : Runnable 과 유사하지만 반환값이 존재&#x20;

```java
@FunctionalInterface
public interface Runnable {
    void run();        //void
}


@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;        //return V 
}

```

### 3-2. Future

* 비동기적인 작업의 현재 상태를 조회하거나 결과를 가져올 수 있다.&#x20;
* [https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html)
* get() : 블로킹 콜이다.&#x20;
  * 타임아웃(최대한 기다릴 시간)을 설정할 수 있다. : get(time, timeunit)&#x20;

```java

public class CallableAndFutureTest {

    @Test
    void get으로_결과값_가져오기() throws ExecutionException, InterruptedException, TimeoutException {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future<String> helloFuture = executorService.submit(() -> {
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello, Future!";
        });

        System.out.println("Started!");

        String message = helloFuture.get();
        assertThat(message).isEqualTo("Hello, Future!");

        System.out.println("Ended!");
        executorService.shutdown();
    }

    @Test
    void get은_타임아웃을_설정할_수_있다() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future<String> helloFuture = executorService.submit(() -> {
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello, Future!";
        });

        //future.get(timeout, timeunit) : timeout 시간이 지나면, TimeoutException
        assertThatThrownBy(() -> helloFuture.get(2, TimeUnit.SECONDS))
                .isInstanceOf(TimeoutException.class);
    }

    @Test
    void isDone으로_작업상태를_확인할_수_있다() throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future<String> helloFuture = executorService.submit(() -> {
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello, Future!";
        });

        assertThat(helloFuture.isDone()).isFalse();
        System.out.println("Started!");

        assertThat(helloFuture.get()).isEqualTo("Hello, Future!");

        assertThat(helloFuture.isDone()).isTrue();
        System.out.println("Ended!");

        executorService.shutdown();
    }

    @Test
    void cancel로_작업을_취소할_수_있다() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future<String> helloFuture = executorService.submit(() -> {
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello, Future!";
        });

        assertThat(helloFuture.isCancelled()).isFalse();
        assertThat(helloFuture.isDone()).isFalse();
        System.out.println("Started!");

        boolean cancel = helloFuture.cancel(false);     //true : 진행중인 스레드 interrupt, false : 현재 진행중인 작업 끝날때까지 wait
        System.out.println("cancel = " + cancel);

        assertThat(helloFuture.isCancelled()).isTrue();
        assertThat(helloFuture.isDone()).isTrue();
        System.out.println("Ended!");

        executorService.shutdown();
    }

    @Test
    void isCanceled로_한번_취소된_서비스는_다시_get_할수없다() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future<String> helloFuture = executorService.submit(() -> {
            try {
                Thread.sleep(3000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello, Future!";
        });

        helloFuture.cancel(false);

        assertThatThrownBy(() -> helloFuture.get())
                .isInstanceOf(CancellationException.class);
    }

    @Test
    void invokeAll로_여러작업을_동시에_실행한다() throws InterruptedException, ExecutionException {
        ExecutorService executorService = Executors.newSingleThreadExecutor();

        Callable<String> hello = () -> {
            Thread.sleep(3000L);
            return "Hello";
        };

        Callable<String> java = () -> {
            Thread.sleep(1000L);
            return "Java";
        };

        Callable<String> spring = () -> {
            Thread.sleep(2000L);
            return "Spring";
        };

        List<Future<String>> futures = executorService.invokeAll(Arrays.asList(hello, java, spring));
        for (Future<String> f : futures) {
            System.out.println(f.get());
        }
        //동시에 출력 : 가장 시간이 긴 Hello 의 종료시간까지 기다렸다가 같이 출력된다.

        //Hello
        //Java
        //Spring
    }

    @Test
    void invokeAny로_여러작업중_하나라도_먼저_응답이_오면_끝낸다() throws InterruptedException, ExecutionException {
        ExecutorService executorService = Executors.newFixedThreadPool(5);

        Callable<String> hello = () -> {
            Thread.sleep(3000L);
            return "Hello";
        };

        Callable<String> java = () -> {
            Thread.sleep(1000L);
            return "Java";
        };

        Callable<String> spring = () -> {
            Thread.sleep(2000L);
            return "Spring";
        };

        String result = executorService.invokeAny(Arrays.asList(hello, java, spring));
        assertThat(result).isEqualTo("Java");   //가장 수행시간이 짧은 Java만 출력된다. 블로킹콜이다.
    }
}
```

<figure><img src="../../.gitbook/assets/image (12) (8) (1).png" alt=""><figcaption></figcaption></figure>

* invokeAll : 가장 수행시간이 긴 작업이 끝날때까지 기다렸다가 한꺼번에 출력한다.&#x20;
* invokeAny : 가장 수행시간이 짧은 작업만 출력한다.&#x20;

## 4. CompletableFuture

### 4-1. 개념

* 자바에서 비동기(asynchronous) 프로그래밍을 가능하게 하는 인터페이스&#x20;
  * Future 를 사용해서도 어느정도는 가능했지만, 하기 힘든 일들이 많았다.&#x20;

### 4-2. 기존의 Future 를 사용하면서 아쉬운 점&#x20;

* Future 를 외부에서 완료시킬 수 없다.&#x20;
  * 취소하거나&#x20;
  * get() 에 타임아웃을 설정하는 방법 밖에는.&#x20;
* 블로킹코드인 get() 을 사용하지 않고서는 작업이 끝났을 때, 콜백을 실행할 수 없다.&#x20;
* 여러 Future 를 조합시킬 수 없다.&#x20;
  * event 의 정보를 가져온 다음&#x20;
  * event 에 참석하는 회원 목록을 가져오기 등&#x20;
* 예외 처리용 API 를 제공하지 않는다.&#x20;

### 4-3. Completable Future&#x20;

* [https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)
* Implements Future
* Implements ​CompletionStage

### 4-4. 비동기로 작업 실행하기&#x20;

* 리턴값이 없는 경우: runAsync()
* 리턴값이 있는 경우: supplyAsync()
* 원하는 Executor(쓰레드풀)를 사용해서 실행할 수도 있다. (기본은 ForkJoinPool.commonPool())

### 4-5. 콜백 제공하기&#x20;

* thenApply(Function): 리턴값을 받아서 다른 값으로 바꾸는 콜백
* thenAccept(Consumer): 리턴값을 또 다른 작업을 처리하는 콜백 (리턴없이)
* thenRun(Runnable): 리턴값 받지 다른 작업을 처리하는 콜백
* 콜백 자체를 또 다른 쓰레드에서 실행할 수 있다.

### 4-6. 조합하기&#x20;

* thenCompose(): 두 작업이 서로 이어서 실행하도록 조합
* thenCombine(): 두 작업을 독립적으로 실행하고 둘 다 종료 했을 때 콜백 실행
* allOf(): 여러 작업을 모두 실행하고 모든 작업 결과에 콜백 실행
* anyOf():여러작업중에가장빨리끝난하나의결과에콜백실행

### 4-7. 예외처리&#x20;

* exeptionally(Function)
* handle(BiFunction):

### 4-8. 코드보기&#x20;

```java
public class CompletableFutureTest {

    @Test
    void completableFuture1() throws ExecutionException, InterruptedException {
        CompletableFuture<String> future = new CompletableFuture<>();
        future.complete("flash");

        assertThat(future.get()).isEqualTo("flash");
    }

    @Test
    void completableFuture2() throws ExecutionException, InterruptedException {
        CompletableFuture<String> future = CompletableFuture.completedFuture("flash");
        assertThat(future.get()).isEqualTo("flash");
    }

    @Test
    void runAsync() {
        CompletableFuture<Void> voidCompletableFuture = CompletableFuture.runAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
        });
        //Hello : ForkJoinPool.commonPool-worker-19
    }

    @Test
    void supplyAsync() throws ExecutionException, InterruptedException {
        CompletableFuture<String> stringCompletableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        });

        System.out.println(stringCompletableFuture.get());
        //Hello : ForkJoinPool.commonPool-worker-19
        //Hello
    }

    @Test
    void anotherExecutor() {
        Executor executor = Executors.newSingleThreadExecutor();
        CompletableFuture.runAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
        }, executor);
        //Hello : pool-1-thread-1   -> 지정된 executor 로 실행된다.
    }

    @Test
    void thenApply_리턴값을받아_새로운값을_리턴() throws ExecutionException, InterruptedException {
        Executor executor = Executors.newSingleThreadExecutor();
        CompletableFuture<String> stringCompletableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        }, executor).thenApply((s) -> {
            System.out.println("thenApply : " + Thread.currentThread().getName());
            return s.toUpperCase(Locale.ROOT);
        });
        System.out.println(stringCompletableFuture.get());
        //Hello : pool-1-thread-1
        //thenApply : pool-1-thread-1
        //HELLO
    }

    @Test
    void thenAccept_리턴값을받아_리턴없이_처리() throws ExecutionException, InterruptedException {
        Executor executor = Executors.newSingleThreadExecutor();
        CompletableFuture<Void> voidCompletableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        }, executor).thenAccept((s) -> {
            System.out.println("thenApply : " + Thread.currentThread().getName());
        });
        System.out.println(voidCompletableFuture.get());
        //Hello : pool-1-thread-1
        //thenApply : pool-1-thread-1
        //null
    }

    @Test
    void thenRun_리턴값을_받아_다른작업을_처리하는_콜백() throws ExecutionException, InterruptedException {
        CompletableFuture<Void> voidCompletableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        }).thenRun(() -> System.out.println(Thread.currentThread().getName()));
        System.out.println("voidCompletableFuture.get() = " + voidCompletableFuture.get());
        //Hello : ForkJoinPool.commonPool-worker-19
        //ForkJoinPool.commonPool-worker-19
        //voidCompletableFuture.get() = null
    }

    @Test
    void thenCompose() throws ExecutionException, InterruptedException {
        CompletableFuture<String> helloFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        });

        CompletableFuture<String> stringCompletableFuture = helloFuture.thenCompose(CompletableFutureTest::getWorldFuture);
        System.out.println(stringCompletableFuture.get());
        //Hello : ForkJoinPool.commonPool-worker-19
        //World : ForkJoinPool.commonPool-worker-5
        //Hello World
    }

    private static CompletableFuture<String> getWorldFuture(String word) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println("World : " + Thread.currentThread().getName());
            return word + " World";
        });
    }

    @Test
    void thenCombine() throws ExecutionException, InterruptedException {
        CompletableFuture<String> helloFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        });

        CompletableFuture<String> worldFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("World : " + Thread.currentThread().getName());
            return "World";
        });

        CompletableFuture<String> result = helloFuture.thenCombine(worldFuture, (s1, s2) -> s1 + " " + s2);
        System.out.println(result.get());
        //Hello : ForkJoinPool.commonPool-worker-19
        //World : ForkJoinPool.commonPool-worker-5
        //Hello World
    }

    @Test
    void allOf() throws ExecutionException, InterruptedException {
        CompletableFuture<String> helloFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        });

        CompletableFuture<String> worldFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("World : " + Thread.currentThread().getName());
            return "World";
        });

        List<CompletableFuture<String>> futures = Arrays.asList(helloFuture, worldFuture);
        CompletableFuture[] futuresArray = futures.toArray(new CompletableFuture[futures.size()]);

        CompletableFuture<List<String>> listCompletableFuture = CompletableFuture.allOf(futuresArray)
                .thenApply(v -> futures.stream()
                        .map(CompletableFuture::join)
                        .collect(Collectors.toList()));

        List<String> strings = listCompletableFuture.get();
        strings.forEach(System.out::println);
        //World : ForkJoinPool.commonPool-worker-5
        //Hello : ForkJoinPool.commonPool-worker-19
        //Hello
        //World
    }

    @Test
    void anyOf() {
        CompletableFuture<String> helloFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        });

        CompletableFuture<String> worldFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println("World : " + Thread.currentThread().getName());
            return "World";
        });

        CompletableFuture.anyOf(helloFuture, worldFuture)
                .thenAccept(System.out::println);
        //World : ForkJoinPool.commonPool-worker-5
        //Hello : ForkJoinPool.commonPool-worker-19
        //World
    }

    @Test
    void exceptionally() throws ExecutionException, InterruptedException {
        boolean isError = true;

        CompletableFuture<String> helloFuture = CompletableFuture.supplyAsync(() -> {
            if (isError) {
                throw new IllegalArgumentException();
            }
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        }).exceptionally((ex) -> {
            System.out.println(ex);
            return "Default Value";
        });

        System.out.println(helloFuture.get());
        //java.util.concurrent.CompletionException: java.lang.IllegalArgumentException
        //Default Value
    }

    @Test
    void handle() throws ExecutionException, InterruptedException {
        boolean isError = true;

        CompletableFuture<String> helloFuture = CompletableFuture.supplyAsync(() -> {
            if (isError) {
                throw new IllegalArgumentException();
            }
            System.out.println("Hello : " + Thread.currentThread().getName());
            return "Hello";
        }).handle((normalResult, exception) -> {
            if (exception != null) {
                System.out.println(exception);
                return "Error!";
            }
            return normalResult;
        });

        System.out.println(helloFuture.get());
        //java.util.concurrent.CompletionException: java.lang.IllegalArgumentException
        //Error!
    }
}
```

### 4-9. 참고 &#x20;

* [https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html)
* [https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)
* [자바에서 비동기 프로그래밍하기](https://velog.io/@pllap/Java%EC%97%90%EC%84%9C%EC%9D%98-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)
