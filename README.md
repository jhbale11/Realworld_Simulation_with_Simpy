# Realworld_Simulation_with_Simpy
![](https://github.com/jhbale11/Realworld_Simulation_with_Simpy/blob/3229a1036db70baa00dcae5e2add8a4d28b5d41f/img/Simpy.png)
Python `SimPy` 라이브러리를 사용하여 현실 세계에 존재할 수 있는 시뮬레이션 환경을 구축하고 실험하는 Repo입니다.

## Installation
[pip](https://pip.pypa.io/en/stable/)를 사용하여 `SimPy`를 설치합니다.
```bash
pip install simpy
```
-----
## Process
```python
def car(env):
    """
    자동차 프로세스 예시
    주차하고 여행을 떠남
    parking과 driving 상태를 스위칭함
    """
    while True:
        print('Start parking at %d' % env.now)
        parking_duration = 5 # 환경에서 timeout 이벤트를 발생시킴(parking_duration동안 휴면)
        yield env.timeout(parking_duration)
    
        print('Start driving at %d' % env.now)
        trip_duration = 2
        yield env.timeout(trip_duration)
    
env = simpy.Environment()
env.process(car(env))
env.run(until=15)
```
- Customer, Vehicles 등 일반적인 `Agent`를 모델링
- 순서가 있는 형태의 프로세스를 의미
- Event Instance를 생성하는 `Generator` 함수를 만듦
    - `Generator` 내부에서 특정 활동이 수행될 때 `Resource`에서 Request를 날려 자원을 일정 시간동안 사용
- Process의 이벤트 시간이 모두 끝나면, 이벤트의 Value를 받을 수 있음
- `timeout()` 주어진 시간 동안 휴면 상태를 유지하는 것을 조절 가능

## Resource
```python
BaseResource(capacity):
    put_queue
    get_queue

    put(): event
    get(): event

## 리소스의 종류
simpy.Resource(env, capacity) # 기본 리소스
simpy.PriorityResource(env, capacity) # 우선 순위가 정해져있는 리소스
simpy.PreemptiveResource(env, capacity) # Preemptive보다 Priority가 우선순위 높음
simpy.Container(env, init, capacity) : # 미분화된 대량 생산이나 대량 소비를 모델링할 수 있음
simpy.Store(env, capacity) # Object의 생산과 소비를 모델링할 수 있음
simpy.FilterStore(env, capacity) # 다양한 속성을 가지는 store 모델링할 수 있음
simpy.PriorityStore(env) # 우선 순위에 따라 필터링할 수 있는 store
```
- 용량이 제한되어 있는 일종의 컨테이너
- 프로세스는 자원에 무언가를 넣거나, 넣으려고 시도함
- 리소스가 꽉 차거나 비어있으면 대기열에서 대기해야 함
- 모든 리소스에는 최대 용량과 두 개의 대기열이 있음
    - 리소스를 넣기 위한 프로세스용 : `put` -> `request`
    - 리소스에서 꺼내기 위한 프로세스용 : `get` -> `request`
- 프로세스가 Resource에 request하고 사용한 후 release함

## Environment
- 시뮬레이션하려는 환경
- `env`는 이벤트 목록에 이벤트들을 저장하고 현재 시뮬레이션 시각을 추적함
```python
import simpy
env = simpy.rt.RealtimeEnvironment(
    factor = 0.1,
    strict = False
)
env.process(Car(env))
env.run(until = 15)
```
-------

## License

[MIT](https://choosealicense.com/licenses/mit/)
