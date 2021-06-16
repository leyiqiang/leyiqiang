---
title: NodeJS poll phrase blocking logic
date: 2019-05-18 17:43:35
tags: [nodeJS, Web Development]
categories: Learning Notes
---

As mentioned in the [NodeJS doc](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/#phases-overview), in poll phrase, node will **block** when appropriate. This post will discuss the block conditions and why this phrase blocks as Node should perfom a **non-blocking** I/O operations.
 <!-- more -->
There are several logics in [libuv core.c](https://github.com/libuv/libuv/blob/v1.x/src/unix/core.c) 

First is the [uv_run](https://github.com/libuv/libuv/blob/v1.x/src/unix/core.c#L348) function, which operates the process of entire event loop:
```c
int uv_run(uv_loop_t* loop, uv_run_mode mode) {
  int timeout;
  int r;
  int ran_pending;

  r = uv__loop_alive(loop);
  if (!r)
    uv__update_time(loop);

  while (r != 0 && loop->stop_flag == 0) {
    uv__update_time(loop);
    uv__run_timers(loop);
    ran_pending = uv__run_pending(loop);
    uv__run_idle(loop);
    uv__run_prepare(loop);

    timeout = 0;
    if ((mode == UV_RUN_ONCE && !ran_pending) || mode == UV_RUN_DEFAULT)
      timeout = uv_backend_timeout(loop);

    uv__io_poll(loop, timeout);
    uv__run_check(loop);
    uv__run_closing_handles(loop);

    if (mode == UV_RUN_ONCE) {
      /* UV_RUN_ONCE implies forward progress: at least one callback must have
       * been invoked when it returns. uv__io_poll() can return without doing
       * I/O (meaning: no callbacks) when its timeout expires - which means we
       * have pending timers that satisfy the forward progress constraint.
       *
       * UV_RUN_NOWAIT makes no guarantees about progress so it's omitted from
       * the check.
       */
      uv__update_time(loop);
      uv__run_timers(loop);
    }

    r = uv__loop_alive(loop);
    if (mode == UV_RUN_ONCE || mode == UV_RUN_NOWAIT)
      break;
  }

  /* The if statement lets gcc compile it to a conditional store. Avoids
   * dirtying a cache line.
   */
  if (loop->stop_flag != 0)
    loop->stop_flag = 0;

  return r;
}
```

`uv__io_poll` takes `timeout` as the second argument. When the event loop runs `UV_RUN_DEFAULT` mode or runs under `UV_RUN_ONCE` mode with no pending query, timeout will be calculated with `uv_backend_timeout` function.
Otherwise it is set to 0.
Here's the [uv_backend_timeout](https://github.com/libuv/libuv/blob/v1.x/src/unix/core.c#L316) code:
```c
int uv_backend_timeout(const uv_loop_t* loop) {
  if (loop->stop_flag != 0)
    return 0;

  if (!uv__has_active_handles(loop) && !uv__has_active_reqs(loop))
    return 0;

  if (!QUEUE_EMPTY(&loop->idle_handles))
    return 0;

  if (!QUEUE_EMPTY(&loop->pending_queue))
    return 0;

  if (loop->closing_handles)
    return 0;

  return uv__next_timeout(loop);
}
```
(`loop->stop_flag` determines whether the event loop is about to exit.) 

Here's the [uv__next_timeout](https://github.com/libuv/libuv/blob/v1.x/src/timer.c#L138) code:
```c
int uv__next_timeout(const uv_loop_t* loop) {
  const struct heap_node* heap_node;
  const uv_timer_t* handle;
  uint64_t diff;

  heap_node = heap_min(timer_heap(loop));
  if (heap_node == NULL)
    return -1; /* block indefinitely */

  handle = container_of(heap_node, uv_timer_t, heap_node);
  if (handle->timeout <= loop->time)
    return 0;

  diff = handle->timeout - loop->time;
  if (diff > INT_MAX)
    diff = INT_MAX;

  return (int) diff;
}
```
Timers are stored in heap, if timer stack is empty, it will block indefinitely.
Otherwise it returns the closest timer, which means that the loop will only be blocked when there are no pending tasks to do and it will wait until the next timeout.

