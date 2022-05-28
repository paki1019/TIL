# Block, Non-Block, Sync, Async

![Block, Non-block, Sync, Async](https://blog.kakaocdn.net/dn/Ueit3/btribLlso0C/z37PUrQlllkF7D3MI5aqb1/img.gif)

- Blocking / Non-blocking 은 호출된 함수가 호출한 함수에게 제어권을 바로 주느냐 안주느냐 차이.
- Sync / Async 는 호출된 함수의 종료를 호출한 함수가 처리하느냐, 호출된 함수가 처리하느냐의 차이.

## Sync VS Async

### Synchronous (동기)

- 동기는 두 가지 이상의 대상(함수, 애플리케이션 등)이 서로 시간을 맞춰 행동하는 것.
- 호출한 함수가 호출된 함수의 작업이 끝나서 결과값을 반환하기를 기다리거나, 지속적으로 호출된 함수에게 확인 요청을하는 경우.

#### A와 B가 시작 시간 또는 종료 시간이 일치하면 동기

- A, B 쓰레드가 동시에 작업을 시작하는 경우
- 메서드 리턴 시간(A)과 결과를 전달받는 시간(B)이 일치하는 경우

![A와 B가 시작 시간 또는 종료 시간이 일치](https://velog.velcdn.com/post-images%2Fcodemcd%2F78dd56a0-4a1d-11ea-99c6-dd89d1c118fe%2FSpringCamp2017-AsyncSpring-1.png)

#### A가 끝나는 시간과 B가 시작하는 시간이 같으면 동기

![A가 끝나는 시간과 B가 시작하는 시간이 같으면 동기](https://velog.velcdn.com/post-images%2Fcodemcd%2F7e027a20-4a1d-11ea-99c6-dd89d1c118fe%2FSpringCamp-AsyncSpring-2.png)

### Asynchronous (비동기)

- 비동기는 동기와 반대로 대상이 서로 시간을 맞추지 않는 것
- 호출하는 함수가 호출되는 함수에게 작업을 맡겨놓고 신경을 쓰지 않는 것

## Blocking VS Non-Blocking

- 블록킹/논블록킹은 직접 제어할 수 없는 대상을 처리하는 방법에 따라 나눔. 직접 제어할 수 없는 대상은 대표적으로 IO, 멀티쓰레드 동기화가 존재.

### Blocking

- Blocking은 직접 제어할 수 없는 대상의 작업이 끝날 때까지 제어권을 넘겨주지 않는 것
- 호출하는 함수가 IO를 요청했을 때 IO처리가 완료될 때까지 아무 일도 하지 못한 채 기다리는 것
-

### Non-Blocking

- Non-Blocking은 Blocking과 반대되는 개념
- 직접 제어할 수 없는 대상의 작업 처리 여부와 상관이 없음
- 호출하는 함수가 IO를 요청한 후 IO처리 완료 여부와 상관없이 바로 자신의 작업을 할 수 있음

## I/O 관점에서 Sync/Async, Blocking/Non-Blocking 예시

### Synchronous Blocking I/O

![Synchronous Blocking I/O](https://velog.velcdn.com/post-images%2Fcodemcd%2F8c51a790-4a1d-11ea-a77e-bff20a601eb8%2Fsynchronous-blocking-IO.png)

```
device = IO.open()
# 이 thread는 데이터를 읽을 때까지 아무 일도 할 수 없음
data = device.read()
print(data)
```

- Synchronous: read() 메서드(애플리케이션)가 리턴하는 시간과 커널에서 결과를 가져오는 시간이 일치
- Blocking: 커널의 작업이 완료될 때까지 대기

### Synchronous Non-Blocking I/O

![Synchronous Non-Blocking I/O](https://velog.velcdn.com/post-images%2Fcodemcd%2F982dd930-4a1d-11ea-a77e-bff20a601eb8%2FSynchronous-non-blocking-IO.png)

```
device = IO.open()
ready = False
while not ready:
    print("There is no data to read!")

    # 다른 작업을 처리할 수 있음

    # while 문 내부의 다른 작업을 다 처리하면 데이터가 도착했는지 확인한다.
    ready = IO.poll(device, IO.INPUT, 5)
data = device.read()
print(data)
```
- Synchronous: read() 메서드(애플리케이션)가 리턴하는 시간과 커널에서 결과를 가져오는 시간이 일치
- Non-Blocking: 애플리케이션으로부터 요청을 받은 커널은 작업 완료 여부와 상관없이 바로 반환하여 제어권을 애플리케이션에게 넘겨줌, 커널의 작업이 완료되면 작업 결과를 애플리케이션에게 반환.

### Asynchronous Non-Blocking I/O (AIO)

![Asynchronous Non-Blocking I/O (AIO)](https://velog.velcdn.com/post-images%2Fcodemcd%2F9dd91ca0-4a1d-11ea-a77e-bff20a601eb8%2FAsynchronous-non-blocking-IO.png)
```
ios = IO.IOService()
device = IO.open(ios)

def inputHandler(data, err):
    "Input data handler"
    if not err:
        print(data)

device.readSome(inputHandler)
# 이 thread는 데이터가 도착했는지 신경쓰지 않고 다른 작업을 처리할 수 있다.
ios.loop()
```

- Asynchronous: readSome() 메서드(애플리케이션)가 리턴하는 시간과 커널에서 결과를 가져오는 시간이 일치하지 않음.
- Non-Blocking: 애플리케이션으로부터 요청을 받은 커널은 작업 완료 여부와 상관없이 바로 반환하여 제어권을 애플리케이션에게 넘겨줌. 작업이 끝나면 애플리케이션에게 시그널 또는 콜백을 보냄.

### Asynchronous Blocking
- Asynchronous Blocking 조합은 비효율적이라 직접적으로 사용하는 모델은 없음.
- Asynchronous Non-Blocking 모델 중에서 Blocking 으로 동작하는 작업이 있는 경우 의도와 다르게 Asynchronous Blocking으로 동작할 때가 있음.(대표적인 예로는 Node.js와 MySQL을 함께 사용하는 경우)

> 출처
>
> - https://jh-7.tistory.com/25
> - https://velog.io/@codemcd/Sync-VS-Async-Blocking-VS-Non-Blocking-sak6d01fhx