---
layout: post
title: "Testing multithreaded code in Java"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Testing multithreaded code is a tough endeavour. The first advice that you get when try to test concurrency is to isolate your concurrent concerns in the code as much as possible. This a general design advice but in this case it's even more mandatory. When you have properly unit tested the logic that is wrapped by the concurrent construct, you can then start thinking in how to test that bit. Otherwise you can spend so many time trying to figure out what's the concurrent problem, being in the end a simple flawed business logic.

Once you got that covered, you can think in your strategy to test concurrent systems. In [Goos](http://www.growing-object-oriented-software.com/) they cover how you can do it. [Here](https://github.com/npryce/goos-code-examples/tree/master/testing-multithreaded-code/src/book/example/threading/races) you can find the code that I'm going to explain:

First, let's have a look into the SUT:

```
/**
 * This class is not properly synchronised, to demonstrate how the {@link MultithreadedStressTester} works.
 */
public class AtomicBigCounter {
    private BigInteger count = BigInteger.ZERO;

    public BigInteger count() {
        return count;
    }

    public void inc() {
        count = count.add(BigInteger.ONE);
    }
}
```

As you can see, this class is not thread safe, as it's exposing some state through inc method, being that state not thread safe (you could use AtomicInteger instead of BigInteger, to make it thread safe). To test that class we'll include a non-concurrent and a concurrent test.

```@Test public void canIncreaseCounter()```

```
@Test public void
    canIncrementCounterFromMultipleThreadsSimultaneously() throws InterruptedException {
        MultithreadedStressTester stressTester = new MultithreadedStressTester(25000);

        stressTester.stress(new Runnable() {
            public void run() {
                counter.inc();
            }
        });

        stressTester.shutdown();

        assertThat("final count", counter.count(), equalTo(BigInteger.valueOf(stressTester.totalActionCount())));
    }
```

The stress tester will exercise the method n loops with m threads. As our method is incrementing by one, we should see that n*m is equal to the counter.count().

The interesting class, though, is the MultithreadedStressTester

```
	public void stress(final Runnable action) throws InterruptedException {
        spawnThreads(action).await();
    }
    
    private CountDownLatch spawnThreads(final Runnable action) {
        final CountDownLatch finished = new CountDownLatch(threadCount);

        for (int i = 0; i < threadCount; i++) {
            executor.execute(new Runnable() {
                public void run() {
                    try {
                        repeat(action);
                    }
                    finally {
                        finished.countDown();
                    }
                }
            });
        }
        return finished;
    }

    private void repeat(Runnable action) {
        for (int i = 0; i < iterationCount; i++) {
            action.run();
        }
    }
```

If you execute that test you will receive different results and sometimes it's even passing! That's because this test is not deterministic, we can't assure how the threads will interleave in every execution. If we want to be as sure as possible that this test finds the possible buf, we should increase the number of threads and iterations, but with the obvious time trade-off.

You can use a more deterministic approach using [Weaver](https://github.com/google/thread-weaver). To understand how it works let's illustrate it with an example. Let's say that we have an in-memory and not thread-safe store:

```private final Map<Level, Scores> scoresByLevel;
```

We have some service that access to a repo that wraps that store:

```
1		Optional<Scores> scoresFromStore = scoreRepo.findBy(score.level());
2          if(scoresFromStore.isPresent()){
3             scoreRepo.update(score.level(), score);
4          }else{
5             scoreRepo.save(score.level(), new Scores().add(score));
6          }
```

That service is a singleton living in a server that spawns a thread per request, so we'd like to execute that piece atomically. We could use the stress test non-deterministic approach or we could use Weaver. If we think deeply in this problem what we want to test is every combination of the following (as an example, Thread 1 executes line 1 in moment x and Thread 2 executes line 1 in moment x, would be T1/1 : T2/1)

* T1/1 : T2/1
* T1/1 : T2/2
* T1/1 : T2/3
* ....
* T1/2 : T2/1
* T1/2 : T2/2
* T1/2 : T2/3
* ....

For instance, we'll have a problem if T1/5 and T2/2, as T1 didn't save yet, and T2 has already got an empty score from store. That means that T1 will save a score in a level and then T2 will do the same, breaking the logic. And that's what Weaver exactly does, it grabs a method and execute the previous combinations by two threads.

If I get rid of preparation code (annotated with @ThreadedBefore), the test code will look like this:

```
    @ThreadedMain
    public void mainThread() {
        scoreService.save(LEVEL_ID, SCORE_VALUE, aUser);
    }

    @ThreadedSecondary
    public void secondThread() {
        scoreService.save(LEVEL_ID, ANOTHER_SCORE_VALUE, aUser);
    }

    @ThreadedAfter
    public void after() {
        Optional<Scores> scores = scoreRepo.findBy(aLevel());
        assertThat(scores.isPresent()).isTrue();
        assertThat(scores.get().contains(aScoreWith(aUser))).isTrue();
        assertThat(scores.get().contains(aDifferentScoreWith(aUser))).isTrue();
    }

    @Test
    public void testThreading() {
        new AnnotatedTestRunner().runTests(this.getClass(), ScoreService.class);
    }
```

This test will fail always, as is deterministic. As you can see testing concurrency is quite hard, and that's why I'm a supporter of modern frameworks that try to hide that hassle into platform or try to overcome the problem thorugh inmutable data. You can read more about it [here](http://olid16.github.io/2015/01/02/concurrency-flavours/)








