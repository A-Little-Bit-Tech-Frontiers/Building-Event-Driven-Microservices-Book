# 이벤트 기반 마이크로서비스 기초

<br>

## 이벤트 브로커 vs 메시지 브로커

메시지 브로커의 구성 요소와 이벤트 브로커의 구성 요소에는 명확한 차이가 있다. **이벤트 브로커는 메시지 브로커 대신 쓸 수 있지만, 메시지 브로커는 이벤트 브로커의 기능을 완전히 대체할 수 없다.** 

**메시지 브로커를 이용하면** 발행/구독 메시지 큐를 통해 네트워크 전체를 통신할 수 있다. **프로듀서가 큐에 메시지를 쓰면 컨슈머는 메시지를 받아 적절히 처리하고 메시지가 소비된 것으로 ACK되면 즉시 또는 짧은 시간 내에 삭제되는 구조**이다. 메시지 브로커는 처음부터 이벤트 브로커와는 다른 종류의 문제를 처리하고자 설계됐다.

이벤트 브로커는 순서대로 쌓은 사실 로그를 제공할 목적으로 설계되었고 메시지 브로커로는 불가능한 두 가지 구체적인 요건을 충족시킨다.

1. 메시지 큐만 제공하는 메시지 브로커에서 메시지는 큐 단위로만 처리되므로 여러 컨슈머가 같은 큐를 소비해도 각자 레코드의 하위 집합(subset)만 받을 수 있다. 따라서 각 컨슈머는 모든 이벤트의 전체 사본을 얻을 수는 없고, 이벤트만 갖고서는 상태를 정확하게 통신할 수 없다.(= 각 컨슈머는 전체 시스템이나 데이터의 '전체 상태'를 완전히 파악하기 어렵다)
이와 달리, **이벤트 브로커는 레코드 장부를 딱 하나만 보관하고 인덱스를 통해 개별 엑세스를 관리하기 때문에 독립적인 여러 컨슈머가 각자 필요한 이벤트를 마음대로 꺼내갈 수 있다.**

2. 둘째, **메시지 브로커는 ACK를 받고 이벤트를 바로 삭제하지만 이벤트 브로커는 업무상 필요한 시간 동안 이벤트를 보존할 수 있다. 메시지 브로커는 소비 직후 이벤트를 삭제하므로 “모든 애플리케이션에 대해 무기한 보관되고 전역 접근이 가능하면서 재연 가능한 단일 진실 공급원”을 제공할 수 없다.**

<br>

## 요약

이벤트 기반 아키텍처에서의 **이벤트 브로커와 메시지 브로커의 차이를 명확하게 파악**했으며,  파티셔닝을 통한 데이터의 병렬처리 및 가용성을 높이는 방식 등 어떤 식으로 이벤트 브로커가 사용되는 지 파악
현재 업무에서는 메시지 브로커인 SNS와 SQS를 이에 접목시켜 읽다보니 아래와 같이 내용파악.

- **이벤트 브로커의 이벤트 스트림(토픽)** vs **AWS SNS**
    이벤트 스트림과 SNS를 비교해볼 수 있을 것 같다.
    - **이벤트 스트림:** 이벤트를 저장하는 로그 파일로 볼 수 있으며, 이벤트의 순서를 보장하고, 토픽 데이터를 여러 파티션에 걸쳐 분산 저장하여 처리 성능과 내구성을 높인다.
    - **SNS:** pub/sub 모델을 기반으로 하는 메시징 서비스로, 메시지를 구독자에게 푸시하는 역할을 한다. SNS는 메시지를 저장하지 않고, 메시지가 발행되면 구독자에게 바로 전송된다.
    
- **컨슈머 그룹** vs **AWS SQS**
    - 컨슈머 그룹: 하나의 토픽을 구독하고, 그룹 내의 각 컨슈머는 토픽의 파티션을 할당받아 메시지를 소비한다. 컨슈머 그룹은 오프셋을 관리하여, 중단 되더라도 해당 지점부터 다시 소비를 이어나갈 수 있다.
    - SQS: 메시지 큐 서비스로, 메시지를 임시로 저장하고, 컨슈머가 폴링 방식으로 메시지를 가져가 처리한다. 큐를 사용하는 메시지 브로커로, ACK를 전달 받으면 즉시 메시지가 삭제된다.

즉, **이벤트 브로커는 메시지의 저장, 순서 보장, 그리고 복잡한 소비 패턴을 지원하는 반면에, 메시지 브로커는 각각 메시지의 빠른 전달과 간단한 큐잉 및 소비 패턴에 초점을 맞추는 것**이다.
