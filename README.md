### resiliece4j
---
https://github.com/resilience4j/resilience4j

```java
Supplier<String> supplier = () -> backendService.doSomething(param1, param2);

Supplier<String> decorateSupplier = Decorators.ofSupplier(supplier)
  .withRetry(Retry.ofDefaults("name"))
  .withCircuitBreaker(CircuitBreaker.ofDefaults("name"))
  .withBulkhead(Bulkhead.ofDefaults("name"));

String result = Try.ofSupplier(decoratedSupplier)
  .recover(throwable -> "Hello from Recovery").get();
  
String result = circuitBreaker.executeSupplier(supplier);



public interface BackendService {
  String doSomething();
}

CircuitBreaker circuitBreaker = CircuitBreaker.ofDefaults("backendName");

Retry retry = Retry.ofDefaults("backendName");

Supplier<String> decoratedSupplier = CircuitBreaker
  .decorateSupplier(circuitBreaker, backendService::doSomething);
  
decoratedSupplier = Retry
  .decorateSupplier(retry, decoratedSupplier);

String result = Try.ofSupplier(decorateSupplier)
  .recover(throwable -> "Hello from Recovery").get();
  
String result = circuitBreaker.executeSupplier(backendService::doSomething);

CircuitBreaker circuitBreaker = CircuitBreaker.ofDefault("testName");
Observable.fromCallable(backendService::doSomething)
  .compose(CircuitBreakerOperator.of(circuitBreaker))

CircuitBreaker circuitBreaker = CircuitBreaker.ofDefualts("testName");
Mono.fromCallable(backendService::doSomething)
  .compose(CircuitBreakerOperator.of(circuitBreaker))
  
  

RateLimiterConfig = RateLimiterConfig.custom()
  .timeoutDuration(Duration.ofMillis(100))
  .limitRefereshPeriod(Duration.ofSeconds(1))
  .limitForPeriod(1)
  .build();
  
RateLimiter rateLimiter = RateLimiter.of("backendName", config);

Supplier<String> restrictedSupplier = RateLimiter
  .decorateSupplier(rateLimiter, backendService::doSomething);
  
Try<String> firstTry = Try.ofSupplier(restrictedSupplier);
assertThat(firstTry.isSuccess()).isTrue();

Try<String> secondTry = Try.of(restrictedSupplier);
assertThat(secondTry.isFailure()).isTrue();
assertThat(secondTry.getCause()).isInstanceOf(RequestNotPermitted.class);


BulkheadConfig config = BulkheadConfig.custom()
  .maxConcurrentCalls(150)
  .maxWaitTime(100)
  .build();
  
Bulkhead bulkhead = Bulkhead.of("backendName", config);

Supplier<String> supplier = Bulkhead
  .decorateSupplier(bulkhead, backendService::doSomething);


ThreadPoolBulkheadConfig config = ThreadPoolBulkheadConfig.custom()
  .maxThreadPoolSize(10)
  .coreThreadPoolSize(2)
  .queueCapacity(20)
  .build();

ThreadPoolBulkhead bulkhead() = ThreadPoolBulkhead.of("backendName", config);

Supplier<CompletionStage<String>> supplier = ThreadPoolBulkhead
  .decorateSupplier(bulkhead, backendService::doSomething);
  
CompletionStage<String> execution = bulkhead
  .executeSupplier(backendService::doSomething);

circuitBreaker.getEventPublisher()
  .onSuccess(event -> logger.info(...))
  .onError(event -> logger.info(...))
  .onIgnoredError(event -> logger.info(...))
  .onStateTransition(event -> logger.info(...))
circuitBreaker.getEventPublisher()
  .onEvent(event -> logger.info(...));
  

RxJava2Adapter.toFlowable(circuitBreaker.getEventPublisher())
  .filter(event -> event.getEventType() == Type.ERROR)
  .cast(CircuitBreakerOnErrorEvent.class)
  .subscribe(event -> logger.info(...))
```

```
```

```
```
