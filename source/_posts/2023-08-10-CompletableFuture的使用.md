---
title: CompletableFuture的使用
date: 2023-08-10 12:17:39
cover: ../img/posts/2023-08-14-自定义SpringBoot-Starter/cover.jpg
tags: [ Java,并发,CompletableFuture ]
categories:
  - Java
---

> CompletableFuture类提供了非常强大的Future的扩展功能，可以帮助我们简化异步编程的复杂性，提供了函数式编程的能力，可以通过回调的方式处理计算结果，并且提供了转换和组合CompletableFuture的方法。

# 总结

<table>
    <tr>
        <th>分类</th>
        <th>方法名</th>
        <th>描述</th>
    </tr>
    <tr>
        <th rowspan="2">创建异步任务</th>
        <td><code>runAsync</code></td>
        <td>不支持返回值</td>
    </tr>
    <tr>
        <td><code>supplyAsync</code></td>
        <td>支持返回值</td>
    </tr>
    <tr>
        <th rowspan="6">创建异步任务</th>
        <td><code>thenRun</code>、<code>thenRunAsync</code></td>
        <td>不接参数、无返回值</td>
    </tr>
    <tr>
        <td><code>thenAccept</code>、<code>thenAcceptAsync</code></td>
        <td>接返回值、无返回值</td>
    </tr>
    <tr>
        <td><code>thenApply</code>、<code>thenApplyAsync</code></td>
        <td>接返回值、有返回值</td>
    </tr>
    <tr>
        <td><code>exceptionally</code>、<code>exceptionallyAsync</code></td>
        <td>接异常、有返回值</td>
    </tr>
    <tr>
        <td><code>whenComplete</code>、<code>whenCompleteAsync</code></td>
        <td>接返回值和异常、无返回值</td>
    </tr>
    <tr>
        <td><code>handle</code>、<code>handleAsync</code></td>
        <td>接返回值和异常、有返回值</td>
    </tr>
    <tr>
        <th rowspan="7">两个任务组合</th>
        <td><code>thenCompose</code>、<code>thenComposeAsync</code></td>
        <td>任务转化</td>
    </tr>
    <tr>
        <td><code>thenCombine</code>、<code>thenCombineAsync</code></td>
        <td>“且”组合、接返回值、有返回值</td>
    </tr>
    <tr>
        <td><code>thenAcceptBoth</code>、<code>thenAcceptBothAsync</code></td>
        <td>“且”组合、接返回值、无返回值</td>
    </tr>
    <tr>
        <td><code>runAfterBoth</code>、<code>runAfterBothAsync</code></td>
        <td>“且”组合、不接参数、无返回值</td>
    </tr>
    <tr>
        <td><code>applyToEither</code>、<code>applyToEitherAsync</code></td>
        <td>“或”组合、接返回值、有返回值</td>
    </tr>
    <tr>
        <td><code>acceptEither</code>、<code>acceptEitherAsync</code></td>
        <td>“或”组合、接返回值、无返回值</td>
    </tr>
    <tr>
        <td><code>runAfterEither</code>、<code>runAfterEitherAsync</code></td>
        <td>“或”组合、不接参数、无返回值</td>
    </tr>
    <tr>
        <th rowspan="2">多个任务组合</th>
        <td><code>anyOf</code></td>
        <td>“任意一个”组合</td>
    </tr>
    <tr>
        <td><code>allOf</code></td>
        <td>“所有”组合</td>
    </tr>
</table>

> 在CompletableFuture中如果使用没有指定线程池的方法，则会使用`ForkJoinPool.commonPool()`作为它的线程池执行异步代码。

# 创建异步任务

### 不支持返回值

- 方法签名
  ```java
  // 使用默认线程池中的线程
  public static CompletableFuture<Void> runAsync(Runnable runnable);
  // 使用自定义线程池中的线程
  public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor); 
  ```
- 示例代码
  ```java
  CompletableFuture.runAsync(() -> {
      // TODO 执行任务
  });
  ```

### 支持返回值

- 方法签名
  ```java
  // 使用默认线程池中的线程
  public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier);
  // 使用自定义线程池中的线程
  public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor); 
  ```

- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      // TODO 执行任务
      return null;
  });
    ```

# 任务完成时回调方法

当CompletableFuture任务执行完成或者抛出异常的时候，可以执行特定的回调方法。

### 不接参数、无返回值

> 前一个任务执行完成后执行；任务之间**无参数传递**，回调方法**无返回值**

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public CompletableFuture<Void> thenRun(Runnable action);
  // 使用默认线程池中的线程
  public CompletableFuture<Void> thenRunAsync(Runnable action);
  // 使用自定义线程池中的线程
  public CompletableFuture<Void> thenRunAsync(Runnable action, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.runAsync(() -> {
  }).thenRun(() -> {
      // TODO 执行任务
  });
  ```

### 接返回值、无返回值

> 前一个任务执行完成后执行；前一个任务会将执行结果**作为参数**，传递到回调方法中，回调方法**无返回值**

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public CompletableFuture<Void> thenAccept(Consumer<? super T> action);
  // 使用默认线程池中的线程
  public CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action);
  // 使用自定义线程池中的线程
  public CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      return "Result";
  }).thenAccept((result) -> {
      // TODO 执行任务
  });
  ```

### 接返回值、有返回值

> 前一个任务执行完成后执行；前一个任务会将执行结果**作为参数**，传递到回调方法中，并且会调方法**有返回值**

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public <U> CompletableFuture<U> thenApply(Function<? super T,? extends U> fn);
  // 使用默认线程池中的线程
  public <U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn);
  // 使用自定义线程池中的线程
  public <U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      return "Result_1";
  }).thenApply(() -> {
      // TODO 执行任务
      return "Result_2";
  });
  ```

### 接异常、有返回值

> 某个任务执行异常时执行；异常任务抛出的异常**作为参数**，传递到回调方法中，并且会调方法**有返回值**

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public CompletableFuture<T> exceptionally(Function<Throwable, ? extends T> fn);
  // 使用默认线程池中的线程
  public CompletableFuture<T> exceptionallyAsync(Function<Throwable, ? extends T> fn);
  // 使用自定义线程池中的线程
  public CompletableFuture<T> exceptionallyAsync(Function<Throwable, ? extends T> fn, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.runAsync(() -> {
      throw new RuntimeException();
  }).exceptionally((exception) -> {
      // TODO 处理异常
      return "ERROR!";
  });
  ```

### 接返回值和异常、无返回值

> 前一个任务执行完成或出现异常时执行；前一个任务会将结果或抛出的异常**作为参数**，传递到回调方法中，回调方法**无返回值**

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public CompletableFuture<T> whenComplete(BiConsumer<? super T, ? super Throwable> action);
  // 使用默认线程池中的线程
  public CompletableFuture<T> whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action);
  // 使用自定义线程池中的线程
  public CompletableFuture<T> whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      throw new RuntimeException();
  }).whenCompleteAsync((result, exception) -> {
      if (exception != null) {
          // TODO 处理异常
      } else {
          // TODO 执行任务
      }
  });
  ```

### 接返回值和异常、有返回值

> 前一个任务执行完成后执行；前一个任务会将结果或抛出的异常**作为参数**，传递到回调方法中，回调方法**有返回值**

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public <U> CompletableFuture<U> handle(BiFunction<? super T, Throwable, ? extends U> fn);
  // 使用默认线程池中的线程
  public <U> CompletableFuture<U> handleAsync(BiFunction<? super T, Throwable, ? extends U> fn);
  // 使用自定义线程池中的线程
  public <U> CompletableFuture<U> handleAsync(BiFunction<? super T, Throwable, ? extends U> fn, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      throw new RuntimeException();
  }).handleAsync((result, exception) -> {
      if (exception != null) {
          // TODO 处理异常
          return "ERROR!";
      } else {
          // 执行任务
          return "SUCCESS!";
      }
  });
  ```

# 两个任务组合

### 任务转化

> 将前一个任务的执行结果**作为参数**进行处理，处理后**返回一个新的任务**

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public <U> CompletableFuture<U> thenCompose(Function<? super T, ? extends CompletionStage<U>> fn);
  // 使用默认线程池中的线程
  public <U> CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn);
  // 使用自定义线程池中的线程
  public <U> CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture<String> task = CompletableFuture.supplyAsync(() -> {
      return "任务一的结果";
  });
  
  CompletableFuture<String> stringCompletableFuture = CompletableFuture.supplyAsync(() -> {
      return "任务二的结果";
  }).thenComposeAsync((result) -> {
      // TODO 执行任务
      return task;
  });
  ```

### “且”组合、接返回值、有返回值

> 将两个任务组合，当两个任务都正常执行完了，将两个任务的结果作为参数，传递到回调方法中，回调方法有返回值

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public <U,V> CompletableFuture<V> thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn);
  // 使用默认线程池中的线程
  public <U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn);
  // 使用自定义线程池中的线程
  public <U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      // TODO 任务一
      return "任务一的结果";
  }).thenCombineAsync(CompletableFuture.supplyAsync(() -> {
      // TODO 任务二
      return "任务二的结果";
  }), (result1, result2) -> {
      // 任务一和任务二都完成时执行的回调方法
      return null;
  });
  ```

### “且”组合、接返回值、无返回值

> 将两个任务组合，当两个任务都正常执行完了，将两个任务的结果作为参数，传递到回调方法中，回调方法无返回值

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public <U> CompletableFuture<Void> thenAcceptBoth(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action);
  // 使用默认线程池中的线程
  public <U> CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action);
  // 使用自定义线程池中的线程
  public <U> CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      // TODO 任务一
      return "任务一的结果";
  }).thenAcceptBothAsync(CompletableFuture.supplyAsync(() -> {
      // TODO 任务二
      return "任务二的结果";
  }), (result1, result2) -> {
      // TODO 任务一和任务二都完成时执行的回调方法
  });
  ```

### “且”组合、不接参数、无返回值

> 将两个任务组合，当两个任务都正常执行完了，执行回调方法，回调方法无返回值

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public CompletableFuture<Void> runAfterBoth(CompletionStage<?> other, Runnable action);
  // 使用默认线程池中的线程
  public CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action);
  // 使用自定义线程池中的线程
  public CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      // TODO 任务一
      return "任务一的结果";
  }).runAfterBothAsync(CompletableFuture.supplyAsync(() -> {
      // TODO 任务二
      return "任务二的结果";
  }), () -> {
      // TODO 任务一和任务二都完成时执行的回调方法
  });
  ```

### “或”组合、接返回值、有返回值

> 将两个任务组合，其中任意一个任务执行完成，将该任务的结果作为参数，传递到回调方法中，回调方法有返回值

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public <U> CompletableFuture<U> applyToEither(CompletionStage<? extends T> other, Function<? super T, U> fn);
  // 使用默认线程池中的线程
  public <U> CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T, U> fn);
  // 使用自定义线程池中的线程
  public <U> CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T, U> fn, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      // TODO 任务一
      return "任务一的结果";
  }).applyToEitherAsync(CompletableFuture.supplyAsync(() -> {
      // TODO 任务二
      return "任务二的结果";
  }), (result) -> {
      // TODO 任务一和任务二任意一个执行完成时执行的回调方法
      return "SUCCESS";
  });
  ```

### “或”组合、接返回值、无返回值

> 将两个任务组合，其中任意一个任务执行完成，将该任务的结果作为参数，传递到回调方法中，回调方法无返回值

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public CompletableFuture<Void> acceptEither(CompletionStage<? extends T> other, Consumer<? super T> action);
  // 使用默认线程池中的线程
  public CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action);
  // 使用自定义线程池中的线程
  public CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      // TODO 任务一
      try {
          Thread.sleep(10);
      } catch (InterruptedException e) {
          throw new RuntimeException(e);
      }
      return "任务一的结果";
  }).acceptEitherAsync(CompletableFuture.supplyAsync(() -> {
      // TODO 任务二
      return "任务二的结果";
  }), (result) -> {
      // TODO 任务一和任务二任意一个执行完成时执行的回调方法
  });
  ```

### “或”组合、不接参数、无返回值

> 将两个任务组合，其中任意一个任务执行完成，执行回调方法，回调方法无返回值

- 方法签名
  ```java
  // 使用调用它的线程上运行（一般是主线程）
  public CompletableFuture<Void> runAfterEither(CompletionStage<?> other, Runnable action);
  // 使用默认线程池中的线程
  public CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action);
  // 使用自定义线程池中的线程
  public CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action, Executor executor);
  ```
- 示例代码
  ```java
  CompletableFuture.supplyAsync(() -> {
      // TODO 任务一
      try {
          Thread.sleep(10);
      } catch (InterruptedException e) {
          throw new RuntimeException(e);
      }
      return "任务一的结果";
  }).runAfterEitherAsync(CompletableFuture.supplyAsync(() -> {
      // TODO 任务二
      return "任务二的结果";
  }), () -> {
      // TODO 任务一和任务二任意一个执行完成时执行的回调方法
  });
  ```

# 多个任务组合

### “任意一个”组合

> 将多个任务组合，其中任意一个任务执行完成即可拿到该任务的执行结果（执行结果为Object类型），或执行接下来的任务

- 方法签名
  ```java
  public static CompletableFuture<Object> anyOf(CompletableFuture<?>... cfs);
  ```
- 示例代码
  ```java
  CompletableFuture<String> task1 = CompletableFuture.supplyAsync(() -> {
      return "任务一的结果";
  });
  
  CompletableFuture<String> task2 = CompletableFuture.supplyAsync(() -> {
      return "任务二的结果";
  });
  
  Object result = CompletableFuture.anyOf(task1, task2).join();
  ```

### “所有”组合

> 将多个任务组合，当所有任务都执行完成后执行接下来的任务

- 方法签名
  ```java
  public static CompletableFuture<Void> allOf(CompletableFuture<?>... cfs);
  ```
- 示例代码
  ```java
  CompletableFuture<String> task1 = CompletableFuture.supplyAsync(() -> {
      return "任务一的结果";
  });
  
  CompletableFuture<String> task2 = CompletableFuture.supplyAsync(() -> {
      return "任务二的结果";
  });
  
  Object join = CompletableFuture.allOf(task1, task2).join();
  ```
