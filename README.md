# Pintos Project 2: Alarm clock-design and implementation
# backup version
## 구현 목표

- Pintos의 thread가 timer_sleep()을 호출하면, 지정된 tick 동안 sleep 상태로 전환되고, 해당 시간이 지나면 자동으로 깨어나도록 구현합니다.
- 기존의 busy-wait(while문) 방식이 아닌, 효율적인 sleep/wake 메커니즘을 구현합니다.

## 주요 구현 내용

1. **Thread 상태 전환**
   - 예시: `alarm-wait.c`의 테스트에서 thread가 `timer_sleep()`을 호출하면, 해당 thread는 BLOCKED 상태로 전환되어야 합니다.
   - 기존의 `timer_sleep()`의 while문을 제거하고, 대신 `thread_sleep()`을 호출하여 thread를 sleep 상태로 만듭니다.

2. **Thread가 깨어나는 시점**
   - 타이머 인터럽트(`timer_interrupt()`)가 발생할 때마다, 깨어나야 할 thread가 있는지 확인합니다.
   - 깨울 thread가 있다면, `thread_awake()`를 호출하여 해당 thread를 READY 상태로 전환합니다.

3. **struct thread 수정**
   - thread가 언제 깨어나야 하는지 저장할 수 있도록, `struct thread`에 wakeup_tick 등의 멤버를 추가합니다.

4. **wait list 추가**
   - sleep 중인 thread들을 관리하기 위해, `thread.c`에 wait list(예: `blocked_list`)를 추가합니다.

5. **핵심 함수 구현**
   - `thread_sleep(int64_t wakeup_tick)`: 현재 thread를 sleep_list에 추가하고 BLOCKED 상태로 전환.
   - `thread_awake(int64_t current_tick)`: sleep_list를 순회하며, 깨어날 시간이 된 thread를 READY 상태로 전환.

6. **그 외**
   - 동기화 및 race condition 방지를 위해 적절히 인터럽트를 disable/enable 합니다.
   - 테스트 통과를 위해 우선순위 스케줄링 등 추가 구현이 필요할 수 있습니다.
