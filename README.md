
# microservices-with-springboot3-and-spring-cloud
https://www.packtpub.com/product/microservices-with-spring-boot-3-and-spring-cloud-third-edition/9781805128694#_ga=2.83151446.1164592500.1693901026-1509796819.1686212940

## 마이크로서비스의 디자인 패턴

### Service discovery

마이크로서비스는 기동시 동적 IP를 할당받는데 클라이언트는 이를 어떻게 알고 요청할 것인가?

- 해결책 - 현재 가용한 마이크로서비스의 IP와 인스턴스를 추적하는 'Service Discovery' 서비스를 등록한다.

- 해결을 위한 요구사항
    + 자동적으로 마이크로서비스를 등록/해지
    + 클라이언트는 논리적인 엔드포인트로 요청을 할 수 있어야 함. 요청은 사용가능한 마이크로서비스 인스턴스에 라우팅 되어야 함.
    + 요청은 가용한 마이크로서비스에 로드밸런싱 되어야 함.
    + 상태가 건강하지 않은 마이크로서비스를 감지해야 하며 이런 인스턴스에는 요청이 라우팅되지 않아야 함.


### Edge Server

외부에 노출할 마이크로서비스와 내부에 남겨질 마이크로서비스를 구분해야하며 악의적인 클라이언트로부터의 요청으로부터 노출된 마이크로서비스는 보호되어야 한다.

- 해결책 - Edge Server를 추가한다.
  + Note: 때로 Edge Server는 리버스 프록시 역할을 하는 것 같이 보이며 떄로는 Service discovery와 통합되어 동적 로드밸런싱을 수행할 수 있다.


- 해결을 위한 요구사항
  + 설정을 통해 허용된 외부 요청만을 마이크로서비스에 라우팅한다.
  + 표준 프로토콜을 사용하고 OAuth, OIDC, JWT tokens, API keys 등 모범사례를 통해 클라이언트를 신뢰할 수 있음을 보완한다.

### Reactive Microservices

전통적으로 자바 개발자는 Blocking I/O를 통해 동기식 통신을 해왔다. (예: RESTful JSON API over HTTP) Blocking I/O를 사용한다는 것은 요청 길이를 위해 OS로부터 스레드를 할당받는다는 의미다.
동시 요청의 수가 증가하면, 서버는 운영 체제에서 사용 가능한 스레드를 모두 소진하여 응답 시간이 길어지거나 서버가 다운되는 등의 문제가 발생할 수 있다. 일반적으로 마이크로서비스 아키텍처를 사용하면 이러한 문제가 더욱 악화된다. 보통 요청을 처리하기 위해 협력하는 여러 마이크로서비스 체인이 사용된다. 요청을 처리하는 데 참여하는 마이크로서비스의 수가 많을수록 사용 가능한 스레드가 빠르게 고갈된다.

- 해결책 - 다른 서비스(예: 데이터베이스 또는 다른 마이크로서비스)에서 처리가 진행되기를 기다리는 동안 스레드가 할당되지 않도록 비차단 I/O(non-blocking I/O)를 사용한다.

- 해결을 위한 요구사항
  + 가능한 경우, 수신자가 처리를 완료할 때까지 기다리지 않고 메시지를 보내는 비동기 프로그래밍 모델을 사용하라.
  + 만약 동기 프로그래밍 모델을 선호한다면, 응답을 기다리는 동안 스레드를 할당하지 않고 비차단 I/O를 사용하여 동기식 요청을 실행할 수 있는 반응형 프레임워크를 사용하라. 이렇게 하면 마이크로서비스가 작업 부하 증가에 대응하기 위해 확장하기 쉬워진다.
  + 마이크로서비스는 탄력적이고 자체 치유 가능하도록 설계되어야 한다. 탄력적이라 함은 의존하는 서비스 중 하나가 실패해도 응답을 생성할 수 있는 능력을 가지는 것이며, 자체 치유 가능하다 함은 실패한 서비스가 다시 운영 중일 때 마이크로서비스가 해당 서비스를 다시 사용할 수 있어야 한다는 것이다.

### Central configuration

전통적으로 애플리케이션은 설정요소와 함께 배포된다. 예를 들어, 환경 변수의 집합이나 구성 정보를 포함하는 파일 등이다. 마이크로서비스 아키텍처를 기반으로 한 시스템 랜드스케이프(마이크로서비스 아키텍처의 경우, 여러 마이크로서비스 인스턴스가 배치되는 방식과 그들 간의 관계)에서는 많은 수의 마이크로서비스 인스턴스가 배치되므로 몇 가지 질문이 제기된다:

> - 실행 중인 모든 마이크로서비스 인스턴스에 대한 현재 구성 정보 전체 그림을 어떻게 얻을 수 있을까?
> - 구성을 업데이트하고 영향을 받는 모든 마이크로서비스 인스턴스가 올바르게 업데이트되었는지 어떻게 확인할 수 있을까?

- 해결책 - 시스템 랜드스케이프에 구성 서버(configuration server)라는 새로운 구성 요소를 추가하여 모든 마이크로서비스의 구성을 저장한다. 아래 다이어그램에 나타낸 것과 같다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_11.png)

- 해결을 위한 요구사항 - 마이크로서비스 그룹의 구성 정보를 한 곳에 저장하면서, 개발(dev), 테스트(test), 품질 보증(QA), 생산(prod) 등과 같은 다른 환경에 대해 다른 설정을 할 수 있도록 한다.

### Centralized log analysis

전통적으로, 애플리케이션은 로그 이벤트를 작성하여 애플리케이션이 실행되는 서버의 로컬 파일 시스템에 저장된 로그 파일에 저장한다. 마이크로서비스 아키텍처를 기반으로 한 시스템 랜드스케이프, 즉, 많은 수의 작은 서버에 배포된 대량의 마이크로서비스 인스턴스가 주어진 경우, 다음과 같은 질문을 할지도 모른다:

> - 각 마이크로서비스 인스턴스가 자신의 로컬 로그 파일에 기록할 때, 시스템 랜드스케이프에서 어떤 일이 일어나고 있는지 전체적인 개요를 어떻게 파악할 수 있을까?
> - 마이크로서비스 인스턴스 중 하나가 문제를 겪고 로그 파일에 오류 메시지를 작성하기 시작하면, 어떻게 알 수 있을까?
> - 사용자들이 문제를 보고하기 시작하면, 관련된 로그 메시지를 어떻게 찾을 수 있을까? 즉, 문제의 근본 원인이 되는 마이크로서비스 인스턴스를 어떻게 식별할 수 있을까? 다음 다이어그램은 이 문제를 설명한다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_12.png)

- 해결책 - 다음 기능을 수행할 수 있는 중앙 집중식 로깅을 관리하는 새로운 컴포넌트를 추가하십시오:

> - 새로운 마이크로서비스 인스턴스를 감지하고 로그 이벤트를 수집
> - 로그 이벤트를 구조화되고 검색 가능한 방식으로 중앙 데이터베이스에 해석 및 저장
> - 로그 이벤트를 쿼리하고 분석하기 위한 API와 그래픽 도구 제공

- 해결을 위한 요구사항
> - 마이크로서비스는 로그 이벤트를 표준 시스템 출력, 즉 stdout에 스트리밍한다. 이렇게 하면 로그 이벤트가 마이크로서비스별 로그 파일에 기록될 때보다 로그 수집기가 로그 이벤트를 찾기 쉬워진다.
> - 마이크로서비스는 '분산 추적 디자인 패턴'에 관한 다음 섹션에서 설명하는 상관 ID로 로그 이벤트를 태그한다.
> - 정규화된 로그 형식이 정의되어, 로그 수집기가 마이크로서비스에서 수집된 로그 이벤트를 중앙 데이터베이스에 저장되기 전에 정규화된 로그 형식으로 변환할 수 있다. 수집된 로그 이벤트를 쿼리하고 분석할 수 있으려면, 정규화된 로그 형식으로 로그 이벤트를 저장해야 한다.


### Distributed tracing

시스템 랜드스케이프에 대한 외부 요청 처리 중 마이크로서비스 간에 흐르는 요청과 메시지를 추적할 수 있어야 한다.
다음은 몇 가지 장애 시나리오의 예다:

> - 사용자들이 특정 실패에 관한 케이스를 제출하기 시작하면, 문제를 일으킨 마이크로서비스, 즉 근본 원인을 어떻게 식별할 수 있을까?
> - 하나의 케이스가 특정 엔티티, 예를 들어 특정 주문 번호와 관련된 문제를 야기한다면, 이 특정 주문을 처리하는 데 관련된 로그 메시지를 어떻게 찾을 수 있을까? 예를 들어, 그것을 처리하는 데 참여한 모든 마이크로서비스에서의 로그 메시지는 어떻게 될까?
> - 사용자들이 너무 긴 응답 시간에 대해 케이스를 제출하기 시작하면, 호출 체인에서 어느 마이크로서비스가 지연을 일으키는지 어떻게 식별할 수 있을까?

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_13.png)

- 해결책 - 협력하는 마이크로서비스 간의 처리를 추적하기 위해, 모든 관련 요청과 메시지가 공통 상관 ID로 표시되고 상관 ID가 모든 로그 이벤트의 일부가 되도록 보장해야 한다. 상관 ID를 기반으로 중앙 집중식 로깅 서비스를 사용하여 모든 관련 로그 이벤트를 찾을 수 있다. 로그 이벤트 중 하나가 비즈니스 관련 식별자에 대한 정보도 포함하고 있다면, 예를 들어 고객, 제품 또는 주문의 ID 등, 우리는 상관 ID를 사용하여 그 비즈니스 식별자에 대한 모든 관련 로그 이벤트를 찾을 수 있다.

협력하는 마이크로서비스의 호출 체인에서 지연을 분석할 수 있으려면, 요청, 응답 및 메시지가 각 마이크로서비스에 들어오고 나갈 때의 타임스탬프를 수집할 수 있어야 한다.

- 해결책을 위한 요구사항
> - 표준화된 이름의 헤더와 같은 잘 알려진 위치에서 모든 들어오는 또는 새로운 요청과 이벤트에 고유한 상관 ID를 할당
마이크로서비스가 외부 요청을 하거나 메시지를 보낼 때, 그것은 반드시 요청과 메시지에 상관 ID를 추가해야 함
> - 모든 로그 이벤트는 사전 정의된 형식으로 상관 ID를 포함해야 하므로 중앙 집중식 로깅 서비스가 로그 이벤트에서 상관 ID를 추출하고 검색 가능하게 만들 수 있음
> - 요청, 응답, 그리고 메시지가 마이크로서비스 인스턴스에 들어오고 나갈 때 추적 기록을 생성해야 함

### Circuit breaker

동기식 상호 통신을 사용하는 마이크로서비스의 시스템 랜드스케이프는 실패의 연쇄에 노출될 수 있다. 한 마이크로서비스가 응답을 중단하면, 그 클라이언트들도 문제를 겪게 되어 자신의 클라이언트로부터의 요청에 대해 응답을 중단할 수 있다. 이 문제는 시스템 랜드스케이프 전체에 재귀적으로 전파되어 주요 부분을 마비시킬 수 있다.

이는 특히 동기 요청이 Blocking I/O를 사용하여 실행되는 경우에 일반적이다. 즉, 요청 처리 중에 기본 운영 체제에서 스레드를 차단한다. 많은 수의 동시 요청과 예상치 못하게 느리게 응답하기 시작하는 서비스가 결합되면, 스레드 풀은 금방 소진되어 호출자가 멈추거나/또는 충돌하게 된다. 이러한 실패는 호출자의 호출자로 불쾌하게 빠르게 확산될 수 있다.

- 해결책 - 호출하는 서비스에 문제가 감지되면 새로운 외부 요청을 방지하는 회로 차단기(Circuit breaker)를 추가한다.

- 해결책을 위한 요구사항
> - 서비스에 문제가 감지되면 회로를 열고 빠르게 실패 처리한다. (타임아웃을 기다리지 않음)
> - 실패 수정을 위한 프로브(반 개방 회로라고도 함); 즉, 서비스가 다시 정상적으로 운영되는지 확인하기 위해 정기적으로 단일 요청을 통과시킨다.
> - 프로브가 서비스가 다시 정상적으로 작동하고 있다는 것을 감지하면 회로(circuit)를 닫는다. 이 기능은 시스템 랜드스케이프가 이러한 종류의 문제에 대해 복원력이 있게 하므로 매우 중요하다; 즉, 자체 치유이다.

다음 다이어그램은 마이크로서비스의 시스템 랜드스케이프 내의 모든 동기 통신이 회로 차단기를 통해 이루어지는 시나리오를 보여준다. 모든 회로 차단기는 닫혀 있어, 요청이 가는 서비스에 문제가 감지된 Microservice E의 회로 차단기를 제외하고는 트래픽을 허용한다. 따라서, 이 회로 차단기는 열려 있으며 빠른 실패 로직을 사용한다; 즉, 실패하는 서비스를 호출하고 타임아웃이 발생하기를 기다리지 않는다. 대신, Microservice E는 즉시 응답을 반환할 수 있으며, 선택적으로 응답하기 전에 일부 폴백 로직을 적용할 수 있다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_14.png)

### Control loop

서버 수에 걸쳐 퍼져 있는 대량의 마이크로서비스 인스턴스가 있는 시스템 랜드스케이프에서는 충돌하거나 멈춘 마이크로서비스 인스턴스와 같은 문제를 수동으로 감지하고 수정하는 것이 매우 어렵다.

- 해결책 - 시스템 랜드스케이프에 새로운 컴포넌트인 컨트롤 루프를 추가한다. 이 과정은 다음과 같이 나타난다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_15.png)

- 해결을 위한 요구사항  

  컨트롤 루프는 시스템 랜드스케이프의 실제 상태를 지속적으로 관찰하고, 운영자가 지정한 원하는 상태와 비교한다. 두 상태가 다르면 실제 상태를 원하는 상태와 동일하게 만들기 위해 행동을 취한다.

  구현 참고사항: 컨테이너의 세계에서는 보통 쿠버네티스와 같은 컨테이너 오케스트레이터를 사용하여 이 패턴을 구현한다. 15장 '쿠버네티스 소개'에서 쿠버네티스에 대해 더 자세히 알아볼 것이다.

### Centralized monitoring and alarms

관찰된 응답 시간과/또는 하드웨어 자원 사용량이 허용할 수 없을 정도로 높아지면, 문제의 근본 원인을 찾아내기가 매우 어려울 수 있다. 예를 들어, 마이크로서비스 별 하드웨어 자원 소비를 분석할 수 있어야 한다.

- 해석책 - 이를 제어하기 위해, 각 마이크로서비스 인스턴스 단위의 하드웨어 자원 사용에 대한 메트릭을 수집할 수 있는 새로운 컴포넌트인 모니터링 서비스를 시스템 랜드스케이프에 추가한다.

- 해결 요구사항
> - 시스템 랜드스케이프에서 사용하는 모든 서버에서 메트릭을 수집할 수 있어야 한다. 이에는 오토 스케일링 서버가 포함된다.
> - 사용 가능한 서버에서 새롭게 실행되는 마이크로서비스 인스턴스를 감지하고 그들로부터 메트릭을 시작하는 것이 가능해야 한다.
> - 수집된 메트릭을 조회하고 분석하기 위한 API와 그래픽 도구를 제공할 수 있어야 한다.
> - 특정 메트릭이 지정된 임계값을 초과하면 알람이 발생하도록 설정할 수 있어야 한다.

다음 스크린샷은 우리가 20장 '마이크로서비스 모니터링'에서 살펴볼 모니터링 도구인 Prometheus에서 메트릭을 시각화하는 Grafana를 보여준다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_16.png)


## 소프트웨어 지원도구

우리는 마이크로서비스에 대한 기대를 충족시키고, 무엇보다 중요하게는 그들과 함께 오는 새로운 도전을 처리하는 데 도움을 줄 수 있는 매우 좋은 오픈 소스 도구들을 가지고 있다.

Design Pattern|Spring Boot|Spring Cloud|Kubernetes|Istio
|---------|---------|--------|-------|-------|
**Service discovery**|Netflix Eureka and Spring Cloud LoadBalancer|Kubernetes kube-proxy and service resources
**Edge server**||Spring Cloud Gateway and Spring Security OAuth|Kubernetes Ingress controller|Istio ingress gateway
**Reactive microservices**|Project Reactor and Spring WebFlux
**Central configuration**||Spring Config Server|Kubernetes ConfigMaps and Secrets
**Centralized log analysis**|||Elasticsearch, Fluentd, and Kibana. Note: Actually not part of Kubernetes, but can easily be deployed and configured together with Kubernetes
**Distributed tracing**|Micrometer Tracing and Zipkin|||Jaeger
**Circuit breaker**|Resilience4j|||Outlier detection
**Control loop**|||Kubernetes controller managers
**Centralized monitoring and alarms**||||Kiali, Grafana, and Prometheus



---
# Chapter 03

### Skeleton code for product-service 
```
spring init \
--boot-version=3.0.4 \
--type=gradle-project \
--java-version=17 \
--packaging=jar \
--name=product-service \
--package-name=se.magnus.microservices.core.product \
--groupId=se.magnus.microservices.core.product \
--dependencies=actuator,webflux \
--version=1.0.0-SNAPSHOT \
product-service

spring init \
--boot-version=3.0.4 \
--type=gradle-project \
--java-version=17 \
--packaging=jar \
--name=review-service \
--package-name=se.magnus.microservices.core.review \
--groupId=se.magnus.microservices.core.review \
--dependencies=actuator,webflux \
--version=1.0.0-SNAPSHOT \
review-service

spring init \
--boot-version=3.0.4 \
--type=gradle-project \
--java-version=17 \
--packaging=jar \
--name=recommendation-service \
--package-name=se.magnus.microservices.core.recommendation \
--groupId=se.magnus.microservices.core.recommendation \
--dependencies=actuator,webflux \
--version=1.0.0-SNAPSHOT \
recommendation-service

spring init \
--boot-version=3.0.4 \
--type=gradle-project \
--java-version=17 \
--packaging=jar \
--name=product-composite-service \
--package-name=se.magnus.microservices.composite.product \
--groupId=se.magnus.microservices.composite.product \
--dependencies=actuator,webflux \
--version=1.0.0-SNAPSHOT \
product-composite-service
```

### Build all microservices.
```
./gradlew build
```


#### Adding RESTful API

##### Build, run, execute and kill
```
./gradlew build

java -jar microservices/product-composite-service/build/libs/*.jar &
java -jar microservices/product-service/build/libs/*.jar &
java -jar microservices/recommendation-service/build/libs/*.jar &
java -jar microservices/review-service/build/libs/*.jar &

curl http://localhost:7001/product/123

curl http://localhost:7000/product-composite/1

curl http://localhost:7000/product-composite/1 -s | jq .

# Verify that a 404 (Not Found) error is returned for a non-existing productId (13)
curl http://localhost:7000/product-composite/13 -i
# Verify that no recommendations are returned for productId 113
curl http://localhost:7000/product-composite/113 -s | jq .
# Verify that no reviews are returned for productId 213
curl http://localhost:7000/product-composite/213 -s | jq .
# Verify that a 422 (Unprocessable Entity) error is returned for a productId that is out of range (-1)
curl http://localhost:7000/product-composite/-1 -i
# Verify that a 400 (Bad Request) error is returned for a productId that is not a number, i.e. invalid format
curl http://localhost:7000/product-composite/invalidProductId -i

kill $(jobs -p)
```

./gradlew build 를 실행하면 보통의 jar파일과 class 파일들만 포함된 plain jar파일이 생성된다. plain jar는 필요치 않기 때문에 각 서비스의 build.gradle에 이하의 코드를 추가했다.

```
jar {
    enabled = false
}
```
참조) https://docs.spring.io/spring-boot/docs/3.0.4/gradle-plugin/reference/htmlsingle/#packaging-executable.and-plain-archives


## Deploying Our Microservices Using Docker

Docker 이미지에서 fat JAR 파일의 최적화되지 않은 패키징을 처리할 때 fat JAR 파일의 콘텐츠를 여러 폴더로 추출할 수 있다.
기본적으로 Spring Boot는 fat JAR 파일을 추출한 후 다음 폴더를 생성한다.

- dependencies, 모든 종속성을 JAR 파일로 포함  
- spring-boot-loader, Spring Boot 애플리케이션을 시작하는 방법을 알고 있는 Spring Boot 클래스를 포함  
- snapshot-dependencies, 만약 있다면 스냅샷 종속성을 포함  
- application, 애플리케이션 클래스 파일과 리소스를 포함  

Spring Boot 문서는 위에 나열된 순서대로 각 폴더마다 하나의 Docker 레이어를 생성하는 것을 권장한다.
JDK 기반 Docker 이미지를 JRE 기반 이미지로 대체하고, fat JAR 파일을 Docker 이미지에서 적절한 레이어로 풀어내는 지시사항을 추가한 Dockerfile은 다음과 같다.

```dockerfile
FROM eclipse-temurin:17.0.5_8-jre-focal as builder
WORKDIR extracted
ADD ./build/libs/*.jar app.jar
RUN java -Djarmode=layertools -jar app.jar extract
FROM eclipse-temurin:17.0.5_8-jre-focal
WORKDIR application
COPY --from=builder extracted/dependencies/ ./
COPY --from=builder extracted/spring-boot-loader/ ./
COPY --from=builder extracted/snapshot-dependencies/ ./
COPY --from=builder extracted/application/ ./
EXPOSE 8080
ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

Dockerfile 작성 후 빌드

```shell
./gradlew :microservices:product-service:build
cd microservices/product-service
docker build -t product-service .
docker images | grep product-service
docker run --rm -p8080:8080 -e "SPRING_PROFILES_ACTIVE=docker" product-service
curl localhost:8080/product/3
```

Running the container in detach mode
```shell
docker run -d -p8080:8080 -e "SPRING_PROFILES_ACTIVE=docker" --name my-prd-srv product-service
docker logs my-prd-srv -f
docker rm -f my-prd-srv
```

docker-compose.yml
```yaml
version: '2.1'
services:
  product:
    build: microservices/product-service
    mem_limit: 512m
    environment:
      - SPRING_PROFILES_ACTIVE=docker
  recommendation:
    build: microservices/recommendation-service
    mem_limit: 512m
    environment:
      - SPRING_PROFILES_ACTIVE=docker
  review:
    build: microservices/review-service
    mem_limit: 512m
    environment:
      - SPRING_PROFILES_ACTIVE=docker
  product-composite:
    build: microservices/product-composite-service
    mem_limit: 512m
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=docker
```

docker compose execute
```shell
./gradlew build
docker-compose build
docker images
docker-compose up -d
docker-compose logs -f
curl localhost:8080/product-composite/123 -s | jq .
docker-compose down
```

automating tests run
```shell
./test-em-all.bash start stop
```




