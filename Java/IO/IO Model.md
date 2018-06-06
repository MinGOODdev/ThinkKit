# IO Model
* Blocking
* Non-Blocking
* Synchronous
* Asynchronous 

## Synchronous (동기)
* 작업을 요청한 후 해당 작업의 결과가 나올 때까지 기다린 후 처리하는 것입니다.
* 특정 IO 작업을 하기 위한 준비가 되었는지에 대해 집중하는 것입니다.
* IO 작업 준비에 대한 이벤트의 발생을 기다렸다가 해당 이벤트가 발생하면 그에 따른 적절한 처리를 합니다.

## Blocking
* IO가 끝날 때까지 대기해야 합니다.
* 끝나기 전에는 함수가 return 되지 않기 때문입니다.
* 커널이 작업을 완료하기 전까지 유저 프로세스는 작업을 중단한 채 대기해야 합니다.
* IO 작업이 CPU 자원을 거의 사용하지 않기 때문에 blocking 방법은 CPU 낭비가 심합니다.

### Synchronous vs Blocking
* 시스템의 반환을 기다리는 동안 대기 큐에 머무는 것이 필수가 아니면 Synchronous
* 시스템의 반환을 기다리는 동안 대기 큐에 머무는 것이 필수라면 Blocking
* 둘 다 시스템의 반환을 기다린다는 측면만 놓고 봤을 떄는 같은 개념입니다.

## Non-blocking
* blocking 방식의 비효율을 극복하고자 만들어진 방식입니다.
* non-blocking 방식은 IO 작업을 진행하는 동안 유저 프로세스의 작업을 중단시키지 않습니다.
* 유저 프로세스가 IO를 처리하기 위해 커널에 함수를 호출(system call)하면, 커널에서 함수의 진행 상황과 상관없이 바로 결과를 return 합니다.
* 이때 반환되는 결과는 반환하는 순간에 가져올 수 있는 데이터에 해당합니다.
    >처음에는 가져올 수 있는 데이터가 없겠지만 시간이 지나면서 가져올 수 있는 데이터가 생겨날 것입니다.
* 이렇게 되면 서버는 클라이언트가 요청한 사이즈에 맞는 데이터를 반환하기 위해 데이터를 축적해야 합니다.
* 데이터 축적이 끝났을 때, 반환되어 클라이언트에서 요청한 사이즈의 데이터를 받아올 수 있게 됩니다.
* 하지만, 이 방식의 문제는 클라이언트가 따로 반환되는 값이 원하는 사이즈가 되었는지 지속적으로 확인해야 한다는 것입니다.
* (polling) 반환되는 데이터가 준비가 되었는지(원하는 값이 되었는지) 확인하는 과정에서 수많은 클라이언트의 요청이 동시 다발적으로 발생할 경우, CPU에 부담이 가게 됩니다.

### Blocking vs Non-blocking
* 애플리케이션 실행시 OS 대기 큐에 들어가면서 요청에 대한 system call이 완료된 후 응답을 보낼 경우 blocking
* 애플리케이션 실행시 OS 대기 큐에 들어가지 않고 실행 여부와 관계 없이 바로 응답을 보낼 경우 non-blocking

## Asynchronous
* Non-blocking의 문제점을 해결하기 위해 고안되었습니다.
* Non-blocking 처럼 애플리케이션에서 데이터가 준비되었는지 계속해서 확인하는 것이 아니라,
* 커널 레벨에서 데이터가 준비되면 콜백 또는 이벤트를 발생시켜 애플리케이션에 알리게 됩니다.
* 유저 레벨의 애플리케이션 스레드는 계속해서 데이터가 준비가 되었는지 확인할 필요없이 다음 작업을 수행하다가 커널에서 이벤트가 발생하면 그 작업에 해당하는 일을 처리하게 됩니다.

### Synchronous vs Asynchronous
* system call의 완료를 기다리면 synchronous
* system call의 완료를 기다리지 않으면 asynchronous

### Non-blocking vs Asynchronous
* system call이 반환될 때, 실행된 결과(데이터)와 함께 반환된 경우 non-blocking
* system call이 반환될 때, 실행된 결과(데이터)와 함께 반환되지 않는 경우 asynchronous
    > asynchronous 방식은 데이터를 콜백 함수 또는 이벤트, signal로 전달하게 되는 것이며,
    non-blocking 방식은 매 system call return에 데이터가 포함되어, return 되는 데이터가 전부 도달했는지 파악하게 됩니다.  