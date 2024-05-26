---
layout: post
date: 23-11-2018
author: Andrzej Jóźwiak
title: An almost immutable data structure leading to an interesting discussion
tags: code-review java immutability refactoring fp functional-programming
disqus: true
---

During a code review at work I found a peculiar piece of code in Java:

```java
class FooTask {
    // ...
    @NonNull
    private final FooDataProvider provider;
    @Nullable
    private FooTask next;
    @NonNull
    private final OnFooCallback callback;
    // other final fields ...

    // constructor and some methods
}
```

On the first glance it doesn't look bad. Except the fact that the field called `next` is not immutable and nullable. In the original piece of code there were more than three fields. All of them where final, so the one single mutable nullable field stood out like a sore thumb!

How would this look like if the `FooTask` was fully immutable? Before doing such exercise it is worth to understand why the code was implemented in the way it was as it could lead to some useful insights.

Just by looking at the structure of the `FooTask`, it is easy to understand that:
- there can be one or more `FooTasks`
- the order of "execution" of `FooTasks` is important
- the code using `FooTask` is supposed to execute the tasks one by one until it finds a `null`


Most probably because someone or something has a reference to the `FooTask`. It probably checks it and then asks the `FooTask` in the processing sequence. Most probably the number of `FooTasks` in the sequence is unknown, this `next` reference needs to be mutable and nullable so that something can be added there at any time.

We can think of a `FooTask` as a building block, usually if we want to make such building block immutable we need to move the mutation somewhere else, make it someone elses problem. This is the same approach we use for side effects and such.

If we don't want to have a `null` as a valid acceptable value we need to somehow model the task differently and have the possibility to mark that there is nothing left to work on.

Java has a very baroque syntax and most often you cannot express things with it succinctly thus we will use a tiny bit of Haskell to write down what we need. Let's list what we need:
- there is a "chain" of tasks
- there can only be task in a chain or an indicator that there are no more tasks

```haskell
data TaskChain = Stage TaskChain | Finish
```

The `|` is symbolizing logical "or" because a `TaskChain` can either be a `Stage` that has a reference to another `TaskChain` or be a `Finish` indicating that there is nothing else to process. Writing this in Java is quite a tedious task (yes pun intended!) but let's try nonetheless.

We need a supertype first:

```java
public abstract class TaskChain {
    public abstract void execute();
}
```

Why an abstract class instead of an interface? Simply this is the only way in Java to have a sealed class hierarchy like in some other languages. Abstract class can have a private constructor and its inner classes have access to this constructor, making them the only thing that can easily extend such an abstract class.

```java
public abstract class TaskChain {
    private TaskChain() {
        // maybe something common for all chain elements here
    }

    // this subtype describes a Task that can have a next stage
    public static class Stage extends TaskChain {
        private final TaskChain next;
        private final OnFooCallback callback;
        private final FooDataProvider provider;
        // other final fields ...

        public Stage(
            final FooDataProvider provider,
            final OnFooCallback callback,
            final TaskChain next
        ) {
            super();
            this.provider = provider;
            this.callback = callback;
            this.next = next;
        }

        @Override
        public void execute() {
            // some business logic for when the task is executed
        }
    }

    public static class Finish extends TaskChain {
        public Finish() {
            super();
        }

        @Override
        public void execute() {
            // some business logic for when the Finish step is executed
        }
    }
}
```

Now, how to use it? Simply if we do not want to process any tasks in a chain we can just use `TaskChain.Finish`:

```java
final TaskChain chain = new TaskChain.Finish();
chain.execute(); // most probably will do nothing
```

If we want to have only a single Task in a Chain then we need to create a single `TaskChain.Stage` and pass a `TaskChain.Finish` as an argument for the `next` task in chain.

```java
final FooDataProvider provider = // ...
final OnFooCallback callback = // ...

final TaskChain chain = new TaskChain.Stage(provider, callback, new TaskChain.Finish());
```


Why not use a list of `FooTask` instead? Keeping a reference to the next "task" in the chain seems needlessly complicated.

Which one is easier to understand? This do-while block:

```java
final FooTask chainOfTasks = ...
FooTask task = chainOfTasks;
do {
    task.execute();
    task = task.next;
} while (task != null);
```

or a simple iteration:

```java
final List<FooTask> chainOfTasks = ...
for (final FooTask task : chainOfTasks) {
    task.execute();
}
```

Everyone without a moment of hesitation would pick the latter. One explanation might be that the tasks are added dynamically and that during task execution or before it is not possible to formulate a lit of tasks (but it sounds strange). Another explanation might be that the author intended the tasks to call the `execute` method of their children on their own. So from the perspective of the caller that has a reference to the `FooTask` it is unknown how many taks will be called.

```java
public abstract class TaskChain {
    private TaskChain() {
        // maybe something common for all chain elements here
    }

    // this subtype describes a Task that can have a next stage
    public static class Stage extends TaskChain {
        private final TaskChain next;
        private final OnFooCallback callback;
        private final FooDataProvider provider;
        // other final fields ...

        public Stage(
            final FooDataProvider provider,
            final OnFooCallback callback,
            final TaskChain next
        ) {
            super();
            this.provider = provider;
            this.callback = callback;
            this.next = next;
        }

        @Override
        public void execute() {
            // some business logic for when the task is executed
            next.execute()
        }
    }

    public static class Finish extends TaskChain {
        public Finish() {
            super();
        }

        @Override
        public void execute() {
            // some business logic for when the Finish step is executed
        }
    }
}
```

The code got bigger and there is no real benefit on using it comparing to the original mutable one. The whole ordeal would be worth it if it would be reusable, indicative of its purpose and it's structure apparent. The reusable part is the execution chain, so let's make one:

```java
interface Task {
    public void run();
}

public abstract class Execution<A extends Task> {
    public static class Stage<A> extends Execution<A> {
        private final Execution<A> next;
        private final A task;

        private Stage(
            final A task,
            final Execution<A> next
        ) {
            super();
            this.task = task;
            this.next = next;
        }

        @Override
        public void execute() {
            task.run();
            next.execute();
        }
    }

    public static class Finish<A> extends Execution<A> {
        private final A task;

        private Finish(
            final A task;
        ) {
            super();
            this.task = task;
        }

        @Override
        public void execute() {
            if (task != null)
                task.run();
        }
    }

    public static <A extends Task> Execution<A> asExecution(A... tasks) {
        Execution<A> execution = null;
        for (int i = tasks.length - 1; i >= 0; i--) {
            if (i == tasks.length - 1) {
                execution = new Finish<>(tasks[i]);
            } else {
                execution = new Stage<>(tasks[i], execution);
            }
        }
        return execution;
    }
}
```

Now the code that we had in the `FooTask` can implement our new `Task` interface and from now on we can reuse the execution for any different tasks that we might implement other then `Foo`.

There is another thing that was not explored so far. Why is the `next` field private?

```java
class FooTask {
    @NonNull
    private final FooDataProvider provider;
    @Nullable
    private FooTask next;
    @NonNull
    private final OnFooCallback callback;
}
```

Is it set in the constructor? What if it is not and the task during its execution is able to create another task as a next step? Then maybe reworking this is not a good idea in such a case? What do you think?