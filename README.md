
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
  product: # hostname
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

## Adding an API Description Using OpenAPI

product-composite-service/application.yml
```yaml
springdoc:
  swagger-ui.path: /openapi/swagger-ui.html
  api-docs.path: /openapi/v3/api-docs
  packagesToScan: se.magnus.microservices.composite.product
  pathsToMatch: /**

api:

  common:
    version: 1.0.0
    title: Sample API
    description: Description of the API...
    termsOfService: MY TERMS OF SERVICE
    license: MY LICENSE
    licenseUrl: MY LICENSE URL

    externalDocDesc: MY WIKI PAGE
    externalDocUrl: MY WIKI URL
    contact:
      name: NAME OF CONTACT
      url: URL TO CONTACT
      email: contact@mail.com

  responseCodes:
    ok.description: OK
    badRequest.description: Bad Request, invalid format of the request. See response message for more information
    notFound.description: Not found, the specified id does not exist
    unprocessableEntity.description: Unprocessable entity, input parameters caused the processing to fail. See response message for more information

  product-composite:

    get-composite-product:
      description: Returns a composite view of the specified product id
      notes: |
        # Normal response
        If the requested product id is found the method will return information regarding:
        1. Base product information
        1. Reviews
        1. Recommendations
        1. Service Addresses\n(technical information regarding the addresses of the microservices that created the response)

        # Expected partial and error responses
        In the following cases, only a partial response be created (used to simplify testing of error conditions)

        ## Product id 113
        200 - Ok, but no recommendations will be returned

        ## Product id 213
        200 - Ok, but no reviews will be returned

        ## Non numerical product id
        400 - A **Bad Request** error will be returned

        ## Product id 13
        404 - A **Not Found** error will be returned

        ## Negative product ids
        422 - An **Unprocessable Entity** error will be returned
```

product-composite-service/ProductCompositeServiceApplication.java
```java
  @Value("${api.common.version}")         String apiVersion;
@Value("${api.common.title}")           String apiTitle;
@Value("${api.common.description}")     String apiDescription;
@Value("${api.common.termsOfService}")  String apiTermsOfService;
@Value("${api.common.license}")         String apiLicense;
@Value("${api.common.licenseUrl}")      String apiLicenseUrl;
@Value("${api.common.externalDocDesc}") String apiExternalDocDesc;
@Value("${api.common.externalDocUrl}")  String apiExternalDocUrl;
@Value("${api.common.contact.name}")    String apiContactName;
@Value("${api.common.contact.url}")     String apiContactUrl;
@Value("${api.common.contact.email}")   String apiContactEmail;

/**
 * Will exposed on $HOST:$PORT/swagger-ui.html
 *
 * @return the common OpenAPI documentation
 */
@Bean
public OpenAPI getOpenApiDocumentation() {
        return new OpenAPI()
        .info(new Info().title(apiTitle)
        .description(apiDescription)
        .version(apiVersion)
        .contact(new Contact()
        .name(apiContactName)
        .url(apiContactUrl)
        .email(apiContactEmail))
        .termsOfService(apiTermsOfService)
        .license(new License()
        .name(apiLicense)
        .url(apiLicenseUrl)))
        .externalDocs(new ExternalDocumentation()
        .description(apiExternalDocDesc)
        .url(apiExternalDocUrl));
        }
```

product-composite-service/ProductCompositeService.java
```java
@Tag(name = "ProductComposite", description = "REST API for composite product information.")
public interface ProductCompositeService {

  /**
   * Sample usage: "curl $HOST:$PORT/product-composite/1".
   *
   * @param productId Id of the product
   * @return the composite product info, if found, else null
   */
  @Operation(
          summary = "${api.product-composite.get-composite-product.description}",
          description = "${api.product-composite.get-composite-product.notes}")
  @ApiResponses(value = {
          @ApiResponse(responseCode = "200", description = "${api.responseCodes.ok.description}"),
          @ApiResponse(responseCode = "400", description = "${api.responseCodes.badRequest.description}"),
          @ApiResponse(responseCode = "404", description = "${api.responseCodes.notFound.description}"),
          @ApiResponse(responseCode = "422", description = "${api.responseCodes.unprocessableEntity.description}")
  })
  @GetMapping(
          value = "/product-composite/{productId}",
          produces = "application/json")
  ProductAggregate getProduct(@PathVariable int productId);
}
```

```shell
./gradlew build && docker-compose build && docker-compose up -d
```

#### Open Swagger UI viewer
http://localhost:8080/openapi/swagger-ui.html

productId에 '123' 입력하고 Execute 해본다.

#### 같은 결과
```shell
curl -X GET "http://localhost:8080/product-composite/123" -H "accept: application/json"
```

## Adding Persistence

#### Product, Recommendation - Spring Data for MongoDB
#### Review - Spring Data for the JPA to access a MySQL database

- 핵심 마이크로서비스에 영속성 레이어 추가하기
- 영속성에 중점을 둔 자동 테스트 작성하기
- 서비스 레이어에서 영속성 레이어 사용하기
- 복합 서비스 API 확장하기
- Docker Compose 환경에 데이터베이스 추가하기
- 새로운 API와 영속성 레이어의 수동 테스트 진행하기
- 마이크로서비스 환경의 자동 테스트 업데이트하기


### *최종 목표 레이어 구조*
![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_06_01.png)

#### testContainers를 사용할 때의 단점은 매번 Docker 컨테이너를 기동하기 때문에 시간이 걸린다는 것이다. 복수의 테스트 케이스를 기동하게 되면 케이스의 수에 따라
시간이 증가하게 되는데 이는 테스트를 수행하는 개발자의 생산성을 저하시키는 요인이 될 수 있다. 이를 해결하기 위해 Single Container Pattern(https://www.testcontainers.org/test_framework_integration/manual_lifecycle_control/#singleton-containers)을
사용할 수 있다. 이 패턴을 따르면 base class는 하나의 MySQL 도커 컨테이너를 기동한다. base class는 Review에서 쓰이는 MySqlTestBase.java이다.

```java
public abstract class MySqlTestBase {
  private static MySQLContainer database =
    new MySQLContainer("mysql:8.0.32").withStartupTimeoutSeconds(300);
  
  static {
    database.start();
  }
  @DynamicPropertySource
  static void databaseProperties(DynamicPropertyRegistry registry) {
    registry.add("spring.datasource.url", database::getJdbcUrl);
    registry.add("spring.datasource.username", database::getUsername);
    registry.add("spring.datasource.password", database::getPassword);
  }
}
```
---
> - 데이터베이스 컨테이너는 앞선 예제와 같은 방식으로 선언되며, 컨테이너가 시작하는 데 5분의 확장 대기 시간이 추가된다.
> - 정적 블록은 JUnit 코드가 호출되기 전에 데이터베이스 컨테이너를 시작하는 데 사용된다.
> - 데이터베이스 컨테이너는 시작할 때 몇 가지 속성을 정의받게 된다. 예를 들어 어떤 포트를 사용할 것인지 등이다. 이러한 동적으로 생성된 속성들을 애플리케이션 컨텍스트에 등록하기 위해, static method인 databaseProperties()가 정의된다. 이 메서드는 @DynamicPropertySource로 주석 처리되어 애플리케이션 컨텍스트 내의 데이터베이스 구성을 오버라이드한다. 예를 들어 application.yml 파일에서 가져온 구성 등이다.
---
> - @DataMongoTest: 이 주석은 테스트가 시작될 때 MongoDB 데이터베이스를 시작한다.
> - @DataJpaTest: 이 주석은 테스트가 시작될 때 SQL 데이터베이스를 시작한다.
> - 기본적으로, Spring Boot는 SQL 데이터베이스에 대한 업데이트를 롤백하여 다른 테스트에 부정적인 영향을 최소화하도록 테스트를 구성한다. 하지만 우리의 경우, 이런 동작은 일부 테스트 실패를 유발하게 될것이다. 따라서, 클래스 레벨의 주석 @Transactional(propagation = NOT_SUPPORTED)을 사용하여 자동 롤백을 비활성화 한다.
---

#### Product - PersistenceTests 테스트
```shell
./gradlew microservices:product-service:test --tests PersistenceTests
```

productId에 @Indexed 어노테이션을 unique = true 로 걸어주었다.  
이렇게 설정하면 productId를 기준으로 indexing 테이블이 생성되고 중복된 productId를 가진 Entity의 생성을 금지한다.  
하지만 중복된 productId를 가진 객체가 잘 저장되는 상황이었다.  
mongodb 3.0 이후부터 컬렉션 라이프 사이클과 퍼포먼스에 영향을 주는 것을 막기 위해서 Index creation을 반드시 명시적으로 enable 해주어야 한다고 한다.  
명시적으로 enable 해주기 위해서는 아래와 같이 application.yml 을 설정한다.
```yaml
# application.yml

spring:
  data:
    mongodb:
      auto-index-creation: true
```

### 수동 테스트 Manual tests of the new APIs and the persistence layer

```shell
./gradlew build && docker-compose build && docker-compose up
# http://localhost:8080/openapi/webjars/swagger-ui/index.html 접속
# /product-composite 임의의 productId 입력 후 Execute 
docker-compose exec mongodb mongosh product-db --quiet --eval "db.products.find()"
docker-compose exec mongodb mongosh recommendation-db --quiet --eval "db.recommendations.find()"
docker-compose exec mysql mysql -uuser -p review-db -e "select * from reviews"

# get, delete swagger api 모두 테스트 가능
```
---
## Developing Reactive Microservices

> - product-composite 마이크로서비스에 의해 제공되는 생성, 읽기, 삭제 서비스는 비차단 동기 API를 기반으로 할 것이다. composite 마이크로서비스는 웹 및 모바일 플랫폼에서 클라이언트를 가정하고 있으며, 시스템 환경을 운영하는 조직 외부에서 오는 클라이언트도 가정하고 있다. 따라서 동기 API가 자연스럽다.
> - core 마이크로서비스가 제공하는 읽기 서비스도 사용자의 응답을 기다리는 종단 사용자가 있으므로 non-blocking 동기 API로 개발될 것이다.
> - core 마이크로서비스가 제공하는 생성 및 삭제 서비스는 이벤트 기반의 비동기 서비스로 개발될 것으로, 이것은 각 마이크로서비스에 전용된 주제에서 생성 및 삭제 이벤트를 수신한다는 뜻이다.
> - composite 마이크로서비스에 의해 제공되는 동기 API들은 집계된 제품 정보를 생성하고 삭제하기 위해 이러한 topic에 대한 생성 및 삭제 이벤트를 발행할 것이다. 만약 게시 작업이 성공하면 202 (수락됨) 응답으로 반환할 것이며, 그렇지 않으면 오류 응답을 반환할 것이다. 202 응답은 일반적인 200 (OK) 응답과 다르게 – 요청이 수락되었지만 완전히 처리되지 않았음을 나타낸다. 대신 처리 과정은 202응답과 독립적으로 비동기적으로 완료될 것이다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_07_01.png)

### Non-blocking REST API 구현

> - API들이 반응형 데이터 타입만 반환하도록 변경하기
> - 서비스 구현을 변경하여 차단 코드를 포함하지 않도록 하기
> - 테스트를 변경하여 반응형 서비스를 테스트할 수 있게 하기
> - 차단 코드 처리 - 아직 차단이 필요한 코드와 비차단 코드를 분리하기


### Blocking code 다루기
리뷰 서비스의 경우, 관계형 데이터베이스에서 데이터에 접근하기 위해 JPA를 사용하는데, 이는 비차단 프로그래밍 모델을 지원하지 않는다. 대신에, blocking 코드를 실행하는 데 능한 스케줄러를 사용하여 한정된 수의 스레드가 있는 전용 스레드 풀에서 blocking 코드를 실행할 수 있다. blocking 코드에 대해 스레드 풀을 사용하면 마이크로서비스에서 사용 가능한 스레드가 소진되는 것을 방지하고, 만약 있다면 마이크로서비스 내의 동시 non-blocking 처리에 영향을 주지 않게 한다.

1. 스케쥴러 빈을 등록한다.
```java
@Autowired
public ReviewServiceApplication(
  @Value("${app.threadPoolSize:10}") Integer threadPoolSize,
  @Value("${app.taskQueueSize:100}") Integer taskQueueSize
) {
  this.threadPoolSize = threadPoolSize;
  this.taskQueueSize = taskQueueSize;
}
@Bean
public Scheduler jdbcScheduler() {
  return Schedulers.newBoundedElastic(threadPoolSize,
    taskQueueSize, "jdbc-pool");
}
```

2. 서비스에서 jdbcScheduler를 주입한다.
```java
@RestController
public class ReviewServiceImpl implements ReviewService {
  private final Scheduler jdbcScheduler;
  @Autowired
  public ReviewServiceImpl(
    @Qualifier("jdbcScheduler")
    Scheduler jdbcScheduler, ...) {
    this.jdbcScheduler = jdbcScheduler;
  }
```

3. 마지막으로 리액티브 구현에서 scheduler 스레드풀을 사용한다.
```java
@Override
public Flux<Review> getReviews(int productId) {
  if (productId < 1) {
    throw new InvalidInputException("Invalid productId: " + 
      productId);
  }
  LOG.info("Will get reviews for product with id={}", 
    productId);
  return Mono.fromCallable(() -> internalGetReviews(productId))
    .flatMapMany(Flux::fromIterable)
    .log(LOG.getName(), FINE)
    .subscribeOn(jdbcScheduler);
}
private List<Review> internalGetReviews(int productId) {
  List<ReviewEntity> entityList = repository.
    findByProductId(productId);
  List<Review> list = mapper.entityListToApiList(entityList);
  list.forEach(e -> e.setServiceAddress(serviceUtil.
    getServiceAddress()));
  LOG.debug("Response size: {}", list.size());
  return list;
}
```

여기서 차단 코드는 internalGetReviews() 메서드에 위치해 있고, Mono.fromCallable() 메서드를 사용해 Mono 객체로 감싸져 있다. getReviews() 메서드는 subscribeOn() 메서드를 사용해서 jdbcScheduler의 스레드 풀에서 차단 코드를 실행하게 된다.  
나중에 테스트를 돌려보면, 리뷰 서비스로부터 로그 출력을 확인할 수 있다. 그리고 SQL 문장들이 스케줄러의 전용 풀에서 실행되는 걸 확인할 수 있다.



### Non-blocking REST APIs in the composite services
> - API를 변경하여 작업이 반응형 데이터 유형만 반환하도록 한다.
> - 서비스 구현을 변경하여 코어 데이터 서비스의 API를 병렬로 호출하고 블로킹되지 않는 방식으로 한다.
> - 통합 레이어를 변경하여 블로킹되지 않는 HTTP 클라이언트를 사용한다.
> - 리액티브 서비스를 테스트할 수 있도록 테스트를 변경한다.


#### *API의 변경 사항*
복합 서비스의 API를 반응형으로 만들기 위해서는 이전에 설명한 코어 서비스의 API에 적용한 것과 동일한 유형의 변경을 적용해야 한다. 이는 getProduct() 메서드의 반환 유형인 ProductAggregate를 Mono<ProductAggregate>로 대체해야 함을 의미한다.

createProduct() 및 deleteProduct() 메서드는 void 대신 Mono<Void>를 반환하도록 업데이트되어야 한다. 그렇지 않으면 오류 응답을 API 호출자에게 전달할 수 없다.

#### *서비스 구현에서의 변경 사항*
세 개의 API를 병렬로 호출하기 위해 서비스 구현은 Mono 클래스의 정적 zip() 메서드를 사용한다. zip 메서드는 여러 개의 병렬 반응형 요청을 처리하고 모든 요청이 완료되면 이들을 함께 묶어준다. 코드는 다음과 같다.

```java
@Override
public Mono<ProductAggregate> getProduct(int productId) {
  return Mono.zip(
    
    values -> createProductAggregate(
      (Product) values[0], 
      (List<Recommendation>) values[1], 
      (List<Review>) values[2], 
      serviceUtil.getServiceAddress()),
      
    integration.getProduct(productId),
    integration.getRecommendations(productId).collectList(),
    integration.getReviews(productId).collectList())
      
    .doOnError(ex -> 
      LOG.warn("getCompositeProduct failed: {}", 
      ex.toString()))
    .log(LOG.getName(), FINE);
}
```
> - zip 메서드의 첫 번째 매개변수는 'values'라는 배열로 응답을 받는 람다 함수다. 이 배열에는 제품, 추천 목록, 리뷰 목록이 포함된다. 세 개의 API 호출로부터의 응답 집계는 이전과 동일한 helper 메서드인 createProductAggregate()가 처리하며, 어떠한 변경도 없다.
> - 람다 함수 이후의 매개변수들은 zip 메서드가 병렬로 호출할 요청들의 목록으로, 각 요청마다 하나씩 Mono 객체이다. 우리 경우에는 통합 클래스의 메서드에 의해 생성된 세 개의 Mono 객체를 보내며, 각각은 각 코어 마이크로서비스에 전송되는 요청에 대응한다.

#### WebClient를 통해 non-blocking HTTP 호출하기
```java
@Override
public Mono<Product> getProduct(int productId) {
  String url = productServiceUrl + "/product/" + productId;
  return webClient.get().uri(url).retrieve()
    .bodyToMono(Product.class)
    .log(LOG.getName(), FINE)
    .onErrorMap(WebClientResponseException.class, 
      ex -> handleException(ex)
    );
}
```

제품 서비스에 대한 API 호출이 HTTP 오류 응답으로 실패하면 전체 API 요청이 실패한다. WebClient의 onErrorMap() 메서드는 우리의 handleException(ex) 메서드를 호출하며, 이 메서드는 HTTP 계층에서 발생하는 HTTP 예외를 우리 자체의 예외, 예를 들어 NotFoundException 또는 InvalidInputException으로 매핑한다.

그러나 제품 서비스에 대한 호출이 성공하지만 추천 또는 리뷰 API에 대한 호출이 실패하는 경우, 전체 요청이 실패하도록 하고 싶지 않다. 대신 가능한 한 많은 정보를 호출자에게 반환하려고 한다. 따라서 이러한 경우에 예외를 전파하는 대신, 추천 혹은 리뷰의 빈 목록을 반환할 것이다. 오류를 억제하기 위해 onErrorResume(error -> empty())을 호출할 것이다. 코드는 다음과 같다.

```java
@Override
public Flux<Recommendation> getRecommendations(int productId) {
  String url = recommendationServiceUrl + "/recommendation?
  productId=" + productId;
  // Return an empty result if something goes wrong to make it 
  // possible for the composite service to return partial responses
  return webClient.get().uri(url).retrieve()
    .bodyToFlux(Recommendation.class)
    .log(LOG.getName(), FINE)
    .onErrorResume(error -> empty());
}
```

### - 이벤트 주도 비동기 서비스 개발

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_07_07.png)

> - 메시징에 대한 도전 문제 처리
> - 토픽과 이벤트 정의
> - Gradle 빌드 파일에서의 변경 사항
> - 코어 서비스에서 이벤트 소비
> - 복합 서비스에서 이벤트 발행

동기 API 호출보다 비동기 메시지 전송을 선호하더라도, 그 자체로 도전적인 문제들이 따른다. 우리는 Spring Cloud Stream을 어떻게 사용하여 이러한 문제 중 일부를 처리할 수 있는지 살펴볼 것이다. Spring Cloud Stream의 다음 기능에 대해 다룬다.

> - 소비자 그룹
> - 재시도와 데드-레터 큐
> - 보장된 순서와 파티션

#### 소비자 그룹
문제는 메시지 소비자의 인스턴스 수를 늘리면, 예를 들어 제품 마이크로서비스의 인스턴스를 두 개 시작하면, 제품 마이크로서비스의 두 인스턴스 모두 동일한 메시지를 소비하게 된다는 것이다. 이는 다음 다이어그램으로 설명된다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_07_08.png)

이로 인해 한 메시지가 두 번 처리되어 데이터베이스에 중복 또는 원치 않는 불일치가 발생할 수 있다. 따라서 우리는 각 메시지를 처리하기 위해 소비자당 하나의 인스턴스만이 필요하다. 이 문제는 다음 다이어그램에서 보여주듯이 소비자 그룹을 도입함으로써 해결할 수 있다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_07_09.png)


Spring Cloud Stream에서는 consumer 측에서 consumer 그룹을 설정할 수 있다. 예를 들어, product 마이크로서비스의 경우 다음과 같이 설정한다.
```yaml
spring.cloud.stream:
  bindings.messageProcessor-in-0:
    destination: products
    group: productsGroup
```

> - Spring Cloud Stream은 기본적으로 함수에 구성을 바인딩하기 위한 명명 규칙을 적용한다. 함수로 전송된 메시지의 경우, 바인딩 이름은 <함수이름>-in-<인덱스> 이다:
    >   + 함수이름은 앞서 예에서의 함수, messageProcessor의 이름이다.
>   + 인덱스는 함수가 여러 입력 또는 출력 인수를 필요로 하는 경우를 제외하고는 0으로 설정된다. 우리는 다중 인수 함수를 사용하지 않으므로, 예제에서 인덱스는 항상 0으로 설정된다.
>   + 나가는 메시지의 경우, 바인딩 이름 규칙은 <함수이름>-out-<인덱스>이다.
> - destination 속성은 메시지가 소비될 토픽의 이름을 지정하며, 이 경우에는 products이다.
> - group 속성은 제품 마이크로서비스의 인스턴스를 추가할 소비자 그룹을 지정하며, 이 예에서는 productsGroup이다. 이것은 products 토픽에 전송된 메시지가 Spring Cloud Stream에 의해 제품 마이크로서비스의 인스턴스 중 하나만 전달되게 함을 의미한다.

#### 재시도와 배달불능 큐
메시지 처리에 실패한 소비자의 경우, 메시지가 성공적으로 처리될 때까지 다시 큐에 넣을 수 있다.
메시지 내용이 유효하지 않다면, 즉 '독성 메시지(poisoned message)'인 경우, 해당 메시지는 다른 메시지를 처리하는 소비자를 막게 되며 이는 수동으로 제거될 때까지 지속된다.
만약 실패가 일시적인 문제로 인한 것이라면, 예를 들어 일시적인 네트워크 오류로 인해 데이터베이스에 접근할 수 없는 경우, 여러 번의 재시도 후에 처리가 성공할 확률이 높다.

메세지가 결함 분석 및 수정을 위해 다른 저장소로 이동되기 전까지 재시도 횟수를 지정할 수 있어야 합니다. 실패한 메세지는 일반적으로 '데드레터 큐(배달불능 큐)'라고 부르는 전용 큐로 옮겨진다.
예를 들어 네트워크 오류와 같은 일시적인 실패 상황에서 인프라 과부하를 피하기 위해서는 재 시도 횟수와 각 재 시도 사이의 시간 간격을 점차 늘려서 구성할 수 있어야 한다.

Spring Cloud Stream에서 이러한 설정은 소비자 측에서 구성할 수 있으며, 예를 들어 product 마이크로서비스에서 보여주듯이 가능하다.

```yaml
spring.cloud.stream.bindings.messageProcessor-in-0.consumer:
  maxAttempts: 3
  backOffInitialInterval: 500
  backOffMaxInterval: 1000
  backOffMultiplier: 2.0
spring.cloud.stream.rabbit.bindings.messageProcessor-in-0.consumer:
  autoBindDlq: true
  republishToDlq: true
spring.cloud.stream.kafka.bindings.messageProcessor-in-0.consumer:
  enableDlq: true
```

위 예시에서, 메시지를 데드레터 큐에 넣기 전에 Spring Cloud Stream이 3번의 재시도를 수행하도록 지정했다.
첫 번째 재시도는 500ms 후에 시도되고, 나머지 두 번의 시도는 1000ms 후에 이루어진다.
데드레터 큐의 사용을 활성화하는 것은 binding-specific이므로, RabbitMQ와 Kafka 각각에 대한 설정이 있다.

#### 보장된 순서와 파티션
만약 비즈니스 로직에서 메시지가 전송된 순서대로 소비되고 처리되어야 한다면, 처리 성능을 높이기 위해 소비자 당 여러 인스턴스를 사용할 수 없다.
예를 들어, consumer 그룹을 사용할 수 없다. 이는 경우에 따라 들어오는 메시지의 처리에서 허용할 수 없는 지연을 초래할 수 있다.
성능과 확장성을 잃지 않으면서 메시지가 전송된 순서대로 전달되도록 하기 위해 파티션을 사용할 수 있다.
대부분의 경우, 메시지 처리에서 엄격한 순서는 동일한 비즈니스 엔티티에 영향을 주는 메시지에 대해서만 필요하다.
예를 들어, 제품 ID 1에 영향을 주는 메시지는 제품 ID 2에 영향을 주는 메시지와 독립적으로 처리될 수 있는 경우가 많다.
즉, 순서가 보장되어야 하는 것은 동일한 제품 ID를 가진 메시지뿐이다.  
이를 해결하기 위해 각 메시지마다 키를 지정할 수 있도록 하여, 키가 같은 메시지 사이의 순서가 유지될 수 있도록 메시징 시스템이 보장할 수 있게 한다.
이것은 토픽 내에서 서브 토픽 또는 파티션으로 알려진 것들을 도입함으로써 해결될 수 있다. 메시징 시스템은 키를 기반으로하여 메시지를 특정 파티션에 배치한다.
같은 키를 가진 메시지들은 항상 같은 파티션에 배치된다. 그리고 메시징 시스템은 같은 파트션이 내의 모든 메세제들의 전달 순서만 보장하면 된다.
이러한 순서 보장을 위해 우리는 consumer 그룹 내에서 각 파트션이 하나의 컨슈머 인스턴스로 구성되도록 설정한다.
파티션의 개수를 증가함으로써 consumer 인스턴스 개수도 증가 가능하며 이렇게 함으로써 전달 순서와 함께 해당 consumer의 성능도 증대된다.

!()[https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_07_10.png]

Spring Cloud Stream에서는 이를 publisher와 consumer 양쪽 모두에서 구성해야 한다. 발행자 측에서는 키와 파티션의 수를 지정해야 한다.
예를 들어, product-composite 서비스의 경우 다음과 같다.

```yaml
spring.cloud.stream.bindings.products-out-0.producer:
  partition-key-expression: headers['partitionKey']
  partition-count: 2
```

이 설정은 키가 partitionKey라는 이름의 메시지 헤더에서 가져와지고 두 개의 파티션을 사용하게 됨을 의미한다.
각 소비자는 메시지를 소비하려는 파티션을 지정할 수 있다. 예를 들어, 상품 마이크로서비스의 경우 다음과 같다.

```yaml
spring.cloud.stream.bindings.messageProcessor-in-0:
  destination: products
  group: productsGroup
  consumer:
    partitioned: true
    instance-index: 0
```

이 설정(instance-index)은 이 소비자가 0번 파티션, 즉 첫 번째 파티션에서만 메시지를 소비하도록 Spring Cloud Stream에 지시한다.

#### *토픽과 이벤트 정의*
메시징 시스템은 일반적으로 헤더와 본문으로 구성된 메시지를 처리한다.
이벤트는 발생한 사건을 설명하는 메시지다. 이벤트의 경우, 메시지 본문은 이벤트 유형, 이벤트 데이터 및 이벤트 발생 시각을 설명하는 데 사용된다.

#### *Gradle 빌드 파일 변경*

#### *core 서비스에서의 이벤트 소비*
애플리케이션에 이러한 변경을 적용하기 위해 다음 단계를 수행해야 한다:

> - 핵심 서비스의 토픽에 게시된 이벤트를 소비하는 메시지 프로세서 선언: 이 프로세서는 특정 토픽에서 발행된 메시지를 소비하고 해당 메시지에 따라 적절한 작업을 수행한다.
> - 서비스 구현체가 반응형 영속성 계층을 사용하도록 변경: 기존의 동기식 데이터 처리 방식 대신, 비동기식 및 반응형 데이터 처리를 지원하는 영속성 계층을 사용하도록 코드를 수정한다.
> - 이벤트 소비에 필요한 설정 추가: 애플리케이션 설정(예: application.properties 또는 application.yml 파일)에서, 메시징 시스템과의 연결 정보, 구독할 토픽 이름 등 필요한 설정을 추가한다.
> - 이벤트의 비동기 처리를 테스트할 수 있도록 테스트 변경: 기존의 동기식 로직 테스트 대신, 비동기적으로 처리되는 이벤트와 그 결과를 검증하는 테스트 코드가 필요한다.

이러한 모든 변경은 애플리케이션의 확장성과 유연성을 높이며, 시스템 전체의 병목 현상을 줄여준다.

- 메세지 프로세서 정의
```java
@Configuration
public class MessageProcessorConfig {
  private final ProductService productService;
  @Autowired
  public MessageProcessorConfig(ProductService productService) 
  {
    this.productService = productService;
  }
  @Bean
  public Consumer<Event<Integer,Product>> messageProcessor() {
    ...
```

Consumer 구현
```java
return event -> {
  switch (event.getEventType()) {
    case CREATE:
      Product product = event.getData();
      productService.createProduct(product).block();
      break;
    case DELETE:
      int productId = event.getKey();
      productService.deleteProduct(productId).block();
      break;
    default:
      String errorMessage = "Incorrect event type: " + 
        event.getEventType() + 
        ", expected a CREATE or DELETE event";
      throw new EventProcessingException(errorMessage);
  }
};
```

productService 빈에서 발생하는 예외를 메시징 시스템으로 전파할 수 있도록 보장하기 위해, productService 빈에서 받은 응답에 block() 메서드를 호출한다.
이는 메시지 프로세서가 기본 데이터베이스에서의 생성 또는 삭제를 완료하기까지 productService 빈을 기다리도록 보장한다.
block() 메서드를 호출하지 않으면 예외를 전파할 수 없고, 메시징 시스템은 실패한 시도를 다시 큐에 넣거나 가능하면 메시지를 dead-letter 큐로 이동할 수 없다. 대신, 메시지는 조용히 드롭된다.
따라서 반응형 프로그래밍과 함께 사용되는 경우에도 서비스 간의 통신이나 데이터 처리 작업에서 오류가 발생한 경우 이러한 오류 상황을 적절히 처리하고 전파하는 것이 중요하다.
이렇게 하면 문제가 발생한 경우 시스템이 적절한 대응을 할 수 있으며, 필요한 경우 해당 작업을 재실행하거나 다른 대책을 마련할 수 있다.

***
일반적으로 block() 메서드를 호출하는 것은 성능 및 확장성 측면에서 나쁜 관행으로 간주된다.
그러나 이 경우에는 위에서 설명한 것처럼 병렬로 들어오는 몇 가지 메시지만 처리하게 되는데, 파티션 당 하나씩이다.
이는 동시에 차단되는 스레드가 몇 개뿐이므로 성능이나 확장성에 부정적인 영향을 미치지 않는다.
그러나 가능한 한 block() 메서드의 사용을 피하고, 대신 비동기 프로그래밍 기법과 반응형 프로그래밍 모델을 사용하는 것이 좋다.
이런 접근 방식은 시스템의 전체적인 처리 용량을 늘리고, 다양한 작업들 사이에서 자원을 효율적으로 공유할 수 있도록 도와준다.
block() 호출의 필요성이 생기면 그것은 종종 설계상의 문제를 나타내므로, 가능하다면 해당 코드 부분을 리팩토링하여 완전히 비동기화하는 것이 좋다.
***


### 소비(consuming) 이벤트를 위한 설정

1. RabbitMQ가 기본 메세징 시스템이며 기본 Content type은 JSON이다.
```yaml
spring.cloud.stream:
  defaultBinder: rabbit
  default.contentType: application/json
```

2. 다음으로, 메시지 프로세서의 입력을 특정 토픽 이름에 바인딩한다.
```yaml
spring.cloud.stream:
  bindings.messageProcessor-in-0:
    destination: products
```

3. 마지막으로 Kafka와 RabbitMQ의 연결정보를 설정한다.
```yaml
spring.cloud.stream.kafka.binder:
  brokers: 127.0.0.1
  defaultBrokerPort: 9092
spring.rabbitmq:
  host: 127.0.0.1
  port: 5672
  username: guest
  password: guest
---
spring.config.activate.on-profile: docker
spring.rabbitmq.host: rabbitmq
spring.cloud.stream.kafka.binder.brokers: kafka
```

### 테스트 코드 변경

핵심 서비스가 이제 엔티티를 생성하고 삭제하기 위한 이벤트를 받게 되므로, 테스트는 이전 장에서처럼 REST API를 호출하는 대신 이벤트를 보내도록 업데이트해야 한다.
테스트 클래스에서 메시지 프로세서를 호출할 수 있도록, 메시지 프로세서 빈을 멤버 변수에 주입한다.

```java
@SpringBootTest
class ProductServiceApplicationTests {
  @Autowired
  @Qualifier("messageProcessor")
  private Consumer<Event<Integer, Product>> messageProcessor;
```

위 코드에서는 Consumer 함수를 주입하는 것뿐만 아니라 @Qualifier 어노테이션을 사용하여 messageProcessor라는 이름을 가진 Consumer 함수를 주입하려고 지정하는 것을 볼 수 있다.
Consumer 함수 인터페이스 선언에서 메시지 프로세서를 호출하기 위해 accept() 메서드를 사용하는 것에 주목하라.
이는 테스트에서 메시징 시스템을 건너뛰고 메시지 프로세서를 직접 호출한다는 것을 의미한다.

```java
  private void sendCreateProductEvent(int productId) {
    Product product = new Product(productId, "Name " + productId, productId, "SA");
    Event<Integer, Product> event = new Event(CREATE, productId, product);
    messageProcessor.accept(event);
  }
  private void sendDeleteProductEvent(int productId) {
    Event<Integer, Product> event = new Event(DELETE, productId, null);
    messageProcessor.accept(event);
  }
```


### 이벤트 전파

1. HTTP 요청의 본문을 기반으로 이벤트 객체를 생성한다.
2. 이벤트 객체가 페이로드로 사용되고, 이벤트 객체의 키 필드가 헤더의 파티션 키로 사용되는 메시지 객체를 생성한다.
3. 도우미 클래스 StreamBridge를 사용하여 원하는 주제에 이벤트를 게시한다.

```java
  @Override
  public Mono<Product> createProduct(Product body) {
    return Mono.fromCallable(() -> {
      sendMessage("products-out-0", 
        new Event(CREATE, body.getProductId(), body));
      return body;
    }).subscribeOn(publishEventScheduler);
  }
  private void sendMessage(String bindingName, Event event) {
    Message message = MessageBuilder.withPayload(event)
      .setHeader("partitionKey", event.getKey())
      .build();
    streamBridge.send(bindingName, message);
  }
```

### 이벤트 전파(publish)를 위한 설정

> - 통합 계층은 도우미 메서드인 sendMessage()를 사용하여 ProductService 인터페이스의 createProduct() 메서드를 구현한다. 이 helper 메서드는 출력 바인딩의 이름과 이벤트 객체를 받는다. 아래의 설정에서 바인딩 이름 'products-out-0'은 제품 서비스의 주제에 바인딩된다.
> - sendMessage()가 블로킹 코드를 사용하기 때문에, streamBridge를 호출할 때 전용 스케줄러인 'publishEventScheduler'가 제공하는 스레드에서 실행된다. 이는 review 마이크로서비스에서 블로킹 JPA 코드를 처리하는 방식과 같다.
> - helper 메서드인 sendMessage()는 Message 객체를 생성하고, 위에서 설명한 대로 페이로드와 partitionKey 헤더를 설정한다. 마지막으로, 이것은 streamBridge 객체를 사용하여 이벤트를 메시징 시스템에 보내고, 그것은 설정에서 정의된 topic에 게시할 것이다.

```yaml
spring.cloud.stream:
  bindings:
    products-out-0:
      destination: products
    recommendations-out-0:
      destination: recommendations
    reviews-out-0:
      destination: reviews
```

파티션을 사용한다면 다음과 같이 파티션 키와 사용할 파티션의 수를 지정한다.

```yaml
spring.cloud.stream.bindings.products-out-0.producer:
  partition-key-expression: headers['partitionKey']
  partition-count: 2
```


## 수동 테스트

- RabbitMQ 사용 (파티션 없음)
- RabbitMQ 사용 (2 파티션)
- Kafka 사용 (2 파티션)

```shell
./gradlew build && docker-compose build && docker-compose up -d
curl -s localhost:8080/actuator/health | jq -r .status
```

### composite product 생성
```shell
body='{"productId":1,"name":"product name C","weight":300, "recommendations":[
{"recommendationId":1,"author":"author 1","rate":1,"content":"content 1"},
 {"recommendationId":2,"author":"author 2","rate":2,"content":"content 2"},
 {"recommendationId":3,"author":"author 3","rate":3,"content":"content 3"}
], "reviews":[
 {"reviewId":1,"author":"author 1","subject":"subject 1","content":"content 1"},
 {"reviewId":2,"author":"author 2","subject":"subject 2","content":"content 2"},
 {"reviewId":3,"author":"author 3","subject":"subject 3","content":"content 3"}
]}'
curl -X POST localhost:8080/product-composite -H "Content-Type: application/json" --data "$body"
```

### RabbitMQ 확인

http://localhost:15672/#/queues 접속 (admin/admin)

각 주제마다 감사 그룹(auditGroup)을 위한 큐, 해당 핵심 마이크로서비스에서 사용하는 컨슈머 그룹을 위한 큐, 그리고 데드레터(dead-letter) 큐가 있는 것을 볼 수 있다. 또한 예상대로 감사 그룹 큐에 메시지가 포함되어 있는 것도 확인할 수 있다.
이러한 구성은 일반적으로 메시징 시스템에서 사용되는 패턴이다. 각 마이크로서비스는 자체적인 컨슈머 그룹을 가지며, 해당 컨슈머 그룹은 특정 주제를 구독하여 메시지를 처리한다.
동시에 감사(audit)나 모니터링 등의 목적으로 별도의 감사 그룹이나 대기열을 사용하는 것도 일반적이다.
데드레터 큐는 일부 메시징 시스템에서 재처리할 수 없거나 처리 중에 오류가 발생한 메시지를 보관하기 위해 사용된다. 이러한 메시지들은 추후 분석하거나 문제 해결을 위해 검토될 수 있다.

![](./images/img.png)

products.auditGroup 큐를 선택하고, 메시지를 확인한다. 이 큐는 감사 그룹을 위한 것이므로, 각 마이크로서비스에서 발생한 이벤트를 포함한다.
이벤트의 페이로드는 JSON 형식이며, 이벤트의 키는 헤더의 partitionKey 키에 저장된다.

#### 저장된 product-composite 정보 조회
```shell
curl -s localhost:8080/product-composite/1 | jq
```

#### product-composite 삭제
```shell
curl -X DELETE localhost:8080/product-composite/1
```

#### docker compose down
```shell
docker-compose down
```

### RabbitMQ with partitions

토픽 당 두개의 파티션을 할당한 RabbitMQ docker compose 파일 일부
```yaml
  product-p1:
    build: microservices/product-service
    mem_limit: 512m
    environment:
      - SPRING_PROFILES_ACTIVE=docker,streaming_partitioned,streaming_instance_1
    depends_on:
      mongodb:
        condition: service_healthy
      rabbitmq:
        condition: service_healthy
```

> - 첫 번째 product 인스턴스에 대해 사용했던 것과 동일한 소스 코드와 Dockerfile을 사용하지만, 다르게 구성한다.
> - 모든 마이크로서비스 인스턴스가 파티션을 사용할 것임을 인지하게 하기 위해, 환경 변수 SPRING_PROFILES_ACTIVE에 Spring 프로필 streaming_partitioned를 추가했다.
> - 두 개의 product 인스턴스를 다른 Spring 프로필을 사용하여 다른 파티션에 할당한다. Spring 프로필 streaming_instance_0은 첫 번째 product 인스턴스에서 사용되며, streaming_instance_1은 두 번째 인스턴스인 product-p1에서 사용된다.
> - ***두 번째 제품 인스턴스는 비동기 이벤트만 처리할 것이다. API 호출에 응답하지 않는다. 그것은 다른 이름인 product-p1(그것의 DNS 이름으로도 사용됨)을 가지고 있으므로, http://product:8080으로 시작하는 URL에 대한 호출에 응답하지 않는다.***

#### 서비스 실행 with partitions
```shell
docker-compose build && docker-compose -f docker-compose-partitions.yml up -d
```

product 정보를 생성하면 아래와 같은 큐 상태를 볼 수 있다.

![](./images/img_2.png)

product를 생성하면 auditGroup에 번갈아가며 큐가 추가되는 것을 확인할 수 있다.

```shell
docker-compose down
unset COMPOSE_FILE
```

### Kafka with two partitions per topic

메세지 시스템을 RabbitMQ에서 Apache Kafka로 변경한다.
이는 단순히 spring.cloud.stream.defaultBinder의 값을 kafka로 변경하면 된다.
docker-compose-kafka.yml 파일을 사용하여 Kafka와 Zookeeper를 설정한다.

```shell

```yaml
kafka:
  image: confluentinc/cp-kafka:7.3.1
  restart: always
  mem_limit: 1024m
  ports:
    - "9092:9092"
  environment:
    - KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181
    - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092
    - KAFKA_BROKER_ID=1
    - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1
  depends_on:
    - zookeeper
zookeeper:
  image: confluentinc/cp-zookeeper:7.3.1
  restart: always
  mem_limit: 512m
  ports:
    - "2181:2181"
  environment:
    - ZOOKEEPER_CLIENT_PORT=2181
```


#### 실행
```shell
docker-compose build && docker-compose -f docker-compose-kafka.yml up -d
```

#### 테스트
이전과 마찬가지로 product를 2건 생성한다.
Kafka는 그래픽 UI가 없으므로 이하 커맨드를 실행하여 리스트를 확인한다.

```shell
docker-compose exec kafka kafka-topics --bootstrap-server localhost:9092 --list
```

![](./images/img_3.png)

> - error로 시작하는 주제들은 데드레터 큐에 해당하는 토픽들이다.
> - RabbitMQ의 경우처럼 auditGroup 그룹을 찾아볼 수 없다. Kafka에서는 소비자가 이벤트를 처리한 후에도 이벤트가 토픽에 보존되므로, 추가적인 auditGroup 그룹이 필요하지 않다.

특정 토픽의 파티션을 보고 싶다면, 예를 들어 products 토픽의 파티션을 보려면 다음과 같이 실행한다.

```shell
docker-compose exec kafka kafka-topics --bootstrap-server localhost:9092 --describe --topic products
```

![](./images/img_4.png)

특정 파티션의 모든 메세지를 보고 싶다면, 예를 들어 products 토픽의 1번 파티션의 모든 메세지를 보려면 다음과 같이 실행한다.

```shell
docker-compose exec kafka kafka-console-consumer --bootstrap-server localhost:9092 --topic products --from-beginning --timeout-ms 1000 --partition 1
```

![](./images/img_5.png)

출력은 이벤트 내용과 함께 타임아웃 예외로 끝난다. 왜냐하면 커맨드에 대한 타임아웃을 1000ms로 지정하여 명령을 중지하기 때문.

```shell
docker-compose down
unset COMPOSE_FILE
```

---

## Spring Cloud

> - Service discovery
> - Edge server
> - Centralized configuration
> - Circuit breaker
> - Distributed tracing


### Spring Cloud의 변천

> - Netflix Eureka, a discovery server
> - Netflix Ribbon, a client-side load balancer
> - Netflix Zuul, an edge server
> - Netflix Hystrix, a circuit breaker

이후 추가 지원 기능

> - HashiCorp Consul과 Apache Zookeeper를 기반으로 한 서비스 디스커버리와 중앙집중식 구성
> - Spring Cloud Stream을 사용한 이벤트 주도형 마이크로서비스
> - Microsoft Azure, Amazon Web Services, Google Cloud Platform 등의 클라우드 제공업체

See: https://spring.io/projects/spring-cloud

Current component|Replaced by
|-------------|-----------|
Netflix Hystrix|Resilience4j
Netflix Hystrix Dashboard/Netflix Turbine|Micrometer and monitoring system
Netflix Ribbon|Spring Cloud LoadBalancer
Netflix Zuul|Spring Cloud Gateway


#### ***본 프로젝트에서 사용된 디자인 패턴과 소프트웨어***
Design pattern|Software component
|-------------|-----------|
Service discovery|Netflix Eureka and Spring Cloud LoadBalancer
Edge server|Spring Cloud Gateway and Spring Security OAuth
Centralized configuration|Spring Cloud Configuration Server
Circuit breaker|Resilience4j
Distributed tracing|Micrometer Tracing and Zipkin


#### - Netflix Eureka for Service discovery


#### - Spring Cloud Gateway as an edge server
![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_08_02.png)

#### - Spring Cloud Config for centralized configuration

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_08_03.png)

> - 마이크로서비스가 시작될 때, 그들은 구성 서버에게 자신의 구성을 요청한다.
> - 구성 서버는 이 경우 Git 저장소에서 구성을 가져온다.
> - 선택적으로, Git 저장소는 Git 커밋이 Git 저장소에 푸시될 때 구성 서버에 알림을 보내도록 설정할 수 있다.
> - 구성 서버는 Spring Cloud Bus를 사용하여 변경 이벤트를 발행한다. 변경에 영향을 받는 마이크로서비스들은 반응하고 업데이트된 구성을 구성 서버에서 검색한다.


#### - Resilience4j for improved resilience

> - Circuit breaker는 원격 서비스가 응답을 중지하면 실패 반응의 연쇄를 방지하는 데 사용된다.
> - Rate limiter는 지정된 시간 동안 서비스에 대한 요청 수를 제한하는 데 사용한다.
> - Bulkhead는 서비스에 대한 동시 요청 수를 제한하는 데 사용된다.
> - Retries는 가끔 발생할 수 있는 임의의 오류를 처리하는 데 사용된다.
> - Time limiter는 느리거나 응답하지 않는 서비스로부터의 응답을 너무 오래 기다리지 않도록 하는데 사용된다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_08_04.png)

1. 서킷 브레이커는 Closed 상태로 시작하여 요청을 처리할 수 있다.
2. 요청이 성공적으로 처리되는 한, 그것은 Closed 상태를 유지한다.
3. 만약 실패가 발생하기 시작하면, 카운터가 증가하기 시작한다.
4. 특정 시간 동안 실패 횟수의 임계값에 도달하면 서킷 브레이커는 트립, 즉 Open 상태로 전환되어 추가 요청을 처리하지 않는다. 실패의 임계값과 시간 기간 모두 설정 가능하다.
5. 대신, 요청은 빠르게 실패하며, 즉시 예외와 함께 반환된다.
6. 설정 가능한 일정 시간 후에 서킷 브레이커는 Half Open 상태로 들어가고 하나의 요청을 프로브(probe)로 통과시켜 실패 문제가 해결되었는지 확인한다.
7. 프로브 요청이 실패하면 서킷 브레이커는 다시 Open 상태로 돌아간다.
8. 프로브 요청이 성공하면 서킷 브레이커는 초기 Closed 상태로 돌아가서 새로운 요청을 처리할 수 있게 된다.

#### - Micrometer Tracing and Zipkin for distributed tracing

마이크로서비스의 협업 시스템 풍경 같은 분산 시스템에서 무슨 일이 일어나고 있는지 이해하려면, 시스템 풍경에 대한 외부 호출을 처리할 때 마이크로서비스 간에 요청과 메시지가 어떻게 흐르는지 추적하고 시각화할 수 있어야 한다.
Micrometer Tracing은 동일한 처리 흐름의 일부인 요청과 메시지/이벤트를 공통 상관 ID로 표시할 수 있다.


---
## 서비스 디스커버리 추가 Using Netflix Eureka

마이크로서비스를 트래킹 할 때 생각해볼 것
> - 새로운 인스턴스는 언제든지 시작될 수 있다.
> - 기존의 인스턴스는 언제든지 응답을 중단하고 결국에는 충돌할 수 있다.
> - 실패한 인스턴스 중 일부는 잠시 후에 정상화되어 다시 트래픽을 받아야 하며, 다른 일부는 그렇지 않고 서비스 레지스트리에서 제거되어야 한다.
> - 일부 마이크로서비스 인스턴스는 시작하는 데 시간이 걸릴 수 있다. 즉, HTTP 요청을 받을 수 있다고 해서 트래픽이 그들로 라우팅되어야 한다는 것은 아니다.
> - 의도하지 않은 네트워크 분할 및 기타 네트워크 관련 오류가 언제든지 발생할 수 있다.

#### Service discovery with Netflix Eureka in Spring Cloud

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_09_04.png)

> - 마이크로서비스 인스턴스가 시작될 때마다 - 예를 들어, Review 서비스 - 그것은 자신을 Eureka 서버 중 하나에 등록한다.
> - 주기적으로, 각 마이크로서비스 인스턴스는 하트비트 메시지를 Eureka 서버에 보내, 마이크로서비스 인스턴스가 정상적으로 작동하고 요청을 받을 준비가 되었다고 알린다.
> - 클라이언트 - 예를 들어, Product Composite 서비스 - 는 사용 가능한 서비스에 대한 정보를 Eureka 서비스에게 정기적으로 요청하는 클라이언트 라이브러리를 사용한다.
> - 클라이언트가 다른 마이크로서비스에 요청을 보내야 할 때, 그것은 이미 클라이언트 라이브러리에서 사용 가능한 인스턴스 리스트를 가지고 있으며 디스커버리 서버에게 묻지 않고 그 중 하나를 선택할 수 있다. 일반적으로, 사용 가능한 인스턴스는 라운드 로빈 방식으로 선택된다. 즉, 첫 번째 것이 다시 호출되기 전에 차례대로 호출된다.

## Setting up a Netflix Eureka server

1. @EnableEurekaServer 어노테이션을 어플리케이션 클래스에 추가한다.
2. 기본 8080 포트 대신 유레카 포트를 8761로 설정한다.
3. docker compose 파일에 이하의 설정을 추가한다.

```yaml
eureka:
  build: spring-cloud/eureka-server
  mem_limit: 512m
  ports:
    - "8761:8761"
```

## 마이크로서비스를 Netflix Eureka 서버에 연결하기

1. spring-cloud-starter-netflix-eureka-client 의존성 추가
```groovy
Implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
```

2. 각 유닛 테스트를 진행할 때 유레카 서버에 의존해서는 안된다. 모든 스프링부트 테스트에서 넷플릭스 유레카를 비활성화 할 것이다. eureka.client.enabled 프로퍼티를 설정함으로써 가능하다.
```java
@SpringBootTest(webEnvironment=RANDOM_PORT, properties = {"eureka.client.enabled=false"})
```

3. 이외에 application.yml 파일의 설정 변경을 참고하는데 주목해야 할 부분은 spring.application.name 프로퍼티이다. 각 마이크로서비스의 버추얼 호스트네임을 부여하는데 사용되는데 이는 유레카 서비스가 각 마이크로서비스를 식별하는 이름으로 사용된다. 유레카 클라이언트는 이 버추얼 호스트네임을 URL로써 마이크로서비스로의 http 호출에 사용될 것이다.


### product-composite 마이크로서비스에서 Eureka 서버를 통해 사용 가능한 마이크로서비스 인스턴스를 조회할 수 있도록 하기 위해서는 다음과 같은 작업을 수행해야 한다.

1. 메인 애플리케이션 클래스인 ProductCompositeServiceApplication에 로드 밸런서를 인식하는 WebClient 빌더를 생성하는 Spring bean을 추가한다.

```java
@Bean
@LoadBalanced
public WebClient.Builder loadBalancedWebClientBuilder() {
    return WebClient.builder();
}
```

2. ProductCompositeIntegration 클래스에서 WebClient.Builder를 주입받는다. 한번 WebClient가 이렇게 빌드되면 싱글턴 인스턴스로서 변하지 않고(immutable) 재사용 가능하다.

```java
private WebClient webClient;
@Autowired
public ProductCompositeIntegration(
  WebClient.Builder webClientBuilder, 
  ...
) {
  this.webClient = webClientBuilder.build();
  ...
}
```

3. 이하와 같은 하드코딩 된 URL을 사용하지 않고도 유레카 서버를 통해 마이크로서비스 인스턴스를 조회할 수 있다.

```yaml
app:
  product-service:
    host: localhost
    port: 7001
  recommendation-service:
    host: localhost
    port: 7002
  review-service:
    host: localhost
    port: 7003
```

4. 하드코딩된 설정을 처리한 통합 클래스인 ProductCompositeIntegration의 해당 코드는 간소화되고, 핵심 마이크로서비스의 API에 대한 기본 URL 선언으로 대체된다.

```java
private static final String PRODUCT_SERVICE_URL = "http://product";
private static final String RECOMMENDATION_SERVICE_URL = "http://recommendation";
private static final String REVIEW_SERVICE_URL = "http://review";
```

앞서 언급된 URL들의 호스트 이름은 실제 DNS 이름이 아니다. 대신, 이들은 마이크로서비스가 Eureka 서버에 자신을 등록할 때 사용하는 가상의 호스트 이름, 즉 spring.application.name 속성의 값이다.


> Netflix Eureka는 다양한 사용 사례에 대해 설정할 수 있는 매우 구성 가능한 디스커버리 서버이며, 강력하고 탄력적이며 장애 허용성 있는 런타임 특성을 제공한다. 이러한 유연성과 견고함의 단점은 거의 압도적인 수의 구성 옵션이 있다는 것이다.
> 다행히도 Netflix Eureka는 대부분의 구성 가능한 파라미터에 대해 좋은 기본값을 제공합니다 - 적어도 운영 환경에서 사용할 때는 그렇다.
> 개발 중에 Netflix Eureka를 사용하는 경우, 기본값은 긴 시작 시간을 초래한다. 예를 들어, 클라이언트가 Eureka 서버에 등록된 마이크로서비스 인스턴스에 처음으로 성공적인 호출을 하는 데 오랜 시간이 걸릴 수 있다.
> 기본 구성 값으로 사용할 때 최대 2분까지의 대기 시간을 경험할 수 있다. 이 대기 시간은 Eureka 서비스와 마이크로서비스가 시작하는 데 걸리는 시간에 추가된다. 이 대기 시간의 원인은 관련 프로세스들이 등록 정보를 상호 간에 동기화해야 하기 때문이다.
> 마이크로서비스 인스턴트들은 Eureka 서버에 등록해야 하고, 클라이언트는 Eureka 서버에서 정보를 수집해야 한다. 이 통신은 주로 심장 박동(heartbeats) 기반으로 이루어지며, 기본적으로 30초마다 일어난다. 업데이트 전파를 지연시키는 몇 가지 캐시도 관여하게 된다.
> 우리는 개발 중에 유용한 이 대기 시간을 최소화하는 구성을 사용할 것입니다.  
> ***운영 환경에서는 기본값부터 시작하여 사용해야 한다!***

#### 유레커 설정 파라미터

> - Eureka 서버에 대한 매개변수, 접두사는 eureka.server.
> - Eureka 서버와 통신하려는 클라이언트를 위한 Eureka 클라이언트의 매개변수, 접두사는 eureka.client.
> - Eureka 서버에 자신을 등록하려는 마이크로서비스 인스턴스를 위한 Eureka 인스턴스의 매개변수, 접두사는 eureka.instance.


#### 유레카 서버의 설정

```yaml
server:
  port: 8761
eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

  server:
    waitTimeInMsWhenSyncEmpty: 0
    response-cache-update-interval-ms: 5000
```

#### 유레카 서버에 접속할 클라이언트 설정

```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
    initialInstanceInfoReplicationIntervalSeconds: 5
    registryFetchIntervalSeconds: 5
  instance:
    leaseRenewalIntervalInSeconds: 5
    leaseExpirationDurationInSeconds: 5
---
spring.config.activate.on-profile: docker
eureka.client.serviceUrl.defaultZone: http://eureka:8761/eureka/
```

eureka.client.serviceUrl.defaultZone 프로퍼티는 유레카 서버를 찾는데 사용된다. 도커없이 로컬에서 실행시는 localhost로, 도커에서 실행시는 호스트네임 eureka로 설정한다.
나머지 매개변수들은 시작 시간을 최소화하고 중지된 마이크로서비스 인스턴스의 등록 취소 시간을 줄이는 데 사용된다.


#### 디스커버리 서비스 테스트

```shell
./gradlew build && docker-compose build
./test-em-all.bash start
```

#### Scaling up

1. 두개의 추가 리뷰 인스턴스를 시작한다.
```shell
docker-compose up -d --scale review=3
```

2. http://localhost:8761/ 유레카 서버를 열어 review 인스턴스가 3개임을 확인한다.

3. review 마이크로서비스가 시작했음을 알 수 있는 또다른 방법은 이하의 커맨드를 입력하는 것이다.
```shell
docker-compose logs review | grep Started 
```

4. 또한 유레카 서비스의 REST API를 이용하여 인스턴스 ID를 확인할 수 있다.
```shell
curl -H "accept:application/json" localhost:8761/eureka/apps -s | jq -r .applications.application[].instance[].instanceId
```

5. test-em-all.bash 스크립트를 참조하면 유레카 REST API에 전달하는 새 테스트 코드를 확인할 수 있다.
```shell
# Verify access to Eureka and that all four microservices are # registered in Eureka
assertCurl 200 "curl -H "accept:application/json" $HOST:8761/eureka/apps -s"
assertEqual 4 $(echo $RESPONSE | jq ".applications.application | length")
```

6. 모든 인스턴스가 올라간 것을 확인했으면, http 리퀘스트를 보내 서비스 어드레스를 확인하는 것으로 클라이언트 사이드의 로드밸런서를 테스트 해보자.
   라운드-로빈 방식으로 하나씩 돌아가며 review 서비스를 호출하는 것을 확인할 수 있다.
```shell
curl localhost:8080/product-composite/1 -s | jq -r .serviceAddresses.rev
```

7. 이하의 커맨드로 review 인스턴스의 로그를 확인할 수 있다.
```shell
docker-compose logs review | grep "Response size"
```

#### Scaling down

1. 예상치 못하게 하나의 인스턴스가 중단된 것을 이하의 커맨드로 시뮬레이션 할 수 있다.
```shell
docker-compose up -d --scale review=2
```

2. 리뷰 인스턴스가 종료된 후에는 API 호출이 실패할 수 있는 짧은 시간이 있다. 이는 사라진 인스턴스에 대한 정보가 클라이언트인 product-composite 서비스로 전파되는 데 걸리는 시간 때문이다.
   이 시간 동안, 클라이언트 측 로드 밸런서는 더 이상 존재하지 않는 인스턴스를 선택할 수 있다. 이러한 현상을 방지하기 위해, 타임아웃과 재시도와 같은 복원력 메커니즘을 사용할 수 있다.
   후에 'Resilience4j를 사용하여 복원력 향상'에서 이를 어떻게 적용하는지 알아볼 것이다. 지금은 curl 명령에 타임아웃을 지정한다. -m 2 옵션을 사용하여 응답을 기다리는 시간이 2초를 초과하지 않도록 한다.
```shell
curl localhost:8080/product-composite/1 -m 2
```


### 유레카 서버 파괴적 테스트

클라이언트가 Eureka 서버가 중지되기 전에 사용 가능한 마이크로서비스 인스턴스에 대한 정보를 읽어온다면, 클라이언트는 문제없이 작동할 것이다. 마이크로서비스는 그 정보를 로컬에 캐시하기 때문이다.
그러나, 새로 생성된 인스턴스의 정보는 클라이언트에게 제공되지 않을 것이고, 실행 중인 인스턴스가 종료된 경우에도 알림을 받지 못할 것이다.
따라서 더 이상 실행되지 않는 인스턴스로의 호출은 실패를 초래할 것이다. 테스트 해보자.


#### 유레카 서버 중단

1. 유레카 서버를 중지하고 review 서비스는 두 개로 남겨둔다.
```shell
docker-compose up -d --scale review=2 --scale eureka=0
```

2. 몇 번 http 호출을 하여 review 서비스에 대한 응답 확인을 한다.
```shell
curl localhost:8080/product-composite/1 -s | jq -r .serviceAddresses.rev
```

#### 새로운 product 서비스 인스턴스 시작

1. 새로운 product 서비스 인스턴스 생성
```shell
docker-compose up -d --scale review=2 --scale eureka=0 --scale product=2
```

2. 여러번 http 호출을 하면서 product 서비스의 IP를 확인한다.
```shell
curl localhost:8080/product-composite/1 -s | jq -r .serviceAddresses.pro
```

새로운 인스턴스의 정보를 유레카 서버로 전달받지 못했기 때문에 하나의 IP만 응답할 것이다.


#### 유레카 서버 재기동

1. 유레카 서버를 재기동한다. review 서비스는 1개로 줄인다.
```shell
docker-compose up -d --scale review=1 --scale eureka=1 --scale product=2
```

2. 이하 요청을 여러번 보내면서 product와 review 서비스의 IP 주소를 확인한다.
```shell
curl localhost:8080/product-composite/1 -s | jq -r .serviceAddresses
```

> - 모든 요청이 남아 있는 review 인스턴스로 호출되는 것은 클라이언트가 두 번째 리뷰 인스턴스가 사라졌음을 감지했음을 보여준다.
> - product 서비스에 대한 호출은 두 개의 제품 인스턴스에 대해 로드 밸런싱되며, 이는 클라이언트가 두 개의 제품 인스턴스가 사용 가능함을 감지했음을 보여준다.

3. 서비스 셧다운
```shell
docker-compose down
```


## Using Spring Cloud Gateway to Hide Microservices behind an Edge Server

Spring Cloud Gateway를 엣지 서버로 사용하여 마이크로서비스 기반 시스템 랜드스케이프에서 어떤 API가 노출되는지 제어하는 방법을 배울 것이다.
공개 API를 가진 마이크로서비스가 엣지 서버를 통해 외부에서 접근 가능하게 되는 방법과, 비공개 API를 가진 마이크로서비스가 마이크로서비스 랜드스케이프 내부에서만 접근 가능하게 되는 방법을 살펴볼 것이다.  
시스템 랜드스케이프에서 이것은 product-composite 서비스와 디스커버리 서버인 Netflix Eureka가 ***엣지 서버를 통해 노출될 것이라는 의미이다.***

### 할 일
> - 우리 시스템 랜드스케이프에 엣지 서버 추가하기
> - Spring Cloud Gateway 설정, 라우팅 규칙 구성 포함
> - 엣지 서버 테스트하기

모든 요청은 엣지서버로 라우팅 될 것이다. 아래의 그림 참조.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_10_01.png)

앞서 보여준 다이어그램에서 볼 수 있듯이, 외부 클라이언트는 모든 요청을 엣지 서버로 보낸다. 엣지 서버는 URL 경로를 기반으로 들어오는 요청을 라우팅할 수 있다.
예를 들어, /product-composite/로 시작하는 URL을 가진 요청은 제품 복합 마이크로서비스로 라우팅되고, /eureka/로 시작하는 URL을 가진 요청은 Netflix Eureka 기반의 디스커버리 서버로 라우팅된다.

> 디스커버리 서비스를 Netflix Eureka와 함께 작동하게 하려면 엣지 서버를 통해 노출시킬 필요가 없다. 내부 서비스는 Netflix Eureka와 직접 통신한다.
> 이를 노출하는 이유는 웹 페이지와 API를 Netflix Eureka의 상태를 확인하고, 디스커버리 서비스에 현재 등록된 인스턴스가 어떤 것인지 확인해야 하는 운영자에게 접근 가능하게 하기 위함이다.


product-composite와 유레카 서버를 외부로 노출시켰던 ports 설정은 엣지 서버를 도입하게 되면 더이상 필요없다.
```yaml
  product-composite:
    build: microservices/product-composite-service
    ports:
      - "8080:8080"
  eureka:
    build: spring-cloud/eureka-server
    ports:
      - "8761:8761"
```


### Spring Cloud Gateway 추가

1. 스프링부트 스켈레톤 프로젝트 생성
2. spring-cloud-starter-gateway 의존성 추가
3. 유레카 서버에 의해 인식되기 위한 spring-cloud-starter-netflix-eureka-client 의존성 추가
4. common 빌드 파일 settings.gradle 에 엣지 서버 프로젝트 추가
```groovy
include ':spring-cloud:gateway'
```
5. Dockerfile 추가
6. docker-compose 파일에 엣지서버 추가
```yaml
gateway:
  environment:
    - SPRING_PROFILES_ACTIVE=docker
  build: spring-cloud/gateway
  mem_limit: 512m
  ports:
    - "8080:8080"
```
7. 모든 요청은 엣지 서버로부터 들어오기 때문에 composite 헬스체크 요청을 product-composite 서비스로부터 엣지 서버로 옮긴다.
8. 라우팅 룰과 그 외 설정을 추가한다. 많은 설정이 있다.

### composite 헬스 체크 추가

엣지 서버를 추가하기로 했기 때문에 모든 외부 헬스체크 요청 또한 엣지 서버를 통하게 된다. 그러므로 모든 마이크로서비스의 헬스체크를 담당하는 composite 헬스체크 또한 product-composite 서비스에서 엣지 서버로 옮겨야 한다.

1. HealthCheckConfiguration 클래스 추가.
```java
  @Bean
  ReactiveHealthContributor healthcheckMicroservices() {
  
    final Map<String, ReactiveHealthIndicator> registry = new LinkedHashMap<>();
    
            registry.put("product",           () -> getHealth("http://product"));
            registry.put("recommendation",    () -> getHealth("http://recommendation"));
            registry.put("review",            () -> getHealth("http://review"));
            registry.put("product-composite", () -> getHealth("http://product-composite"));
    
            return CompositeReactiveHealthContributor.fromMap(registry);
            }
    
    private Mono<Health> getHealth(String baseUrl) {
            String url = baseUrl + "/actuator/health";
            LOG.debug("Setting up a call to the Health API on URL: {}", url);
            return webClient.get().uri(url).retrieve().bodyToMono(String.class)
            .map(s -> new Health.Builder().up().build())
            .onErrorResume(ex -> Mono.just(new Health.Builder().down(ex).build()))
            .log(LOG.getName(), FINE);
  }
```

2. 메인클래스에는 WebClient.Builder 빈을 추가한다.
```java
  @Bean
  @LoadBalanced
  public WebClient.Builder loadBalancedWebClientBuilder() {
    return WebClient.builder();
  }
```
보이듯이 WebClient.Builder는 @LoadBalanced 어노테이션을 추가함으로써 Eureka 서버를 통해 마이크로서비스 인스턴스를 찾는 데 사용된다.


### Spring Cloud Gateway 설정

1. Spring Cloud Gateway는 Netflix Eureka를 통해 마이크로서비스를 탐색할 것이므로, 다른 서비스들과 마찬가지로 유레카 클라이언트로 설정되어야 한다.
2. 개발용 목적으로 스프링부트 액츄에이터 설정을 추가한다.
```yaml
management.endpoint.health.show-details: "ALWAYS"
management.endpoints.web.exposure.include: "*"
```

3. Spring Cloud Gateway의 내부 프로세스 중 관심있는 부분의 로그 메세지를 보기 위해 로그 레벨을 설정한다. 예를들어, 들어오는 요청에 대해 어디로 라우팅할 것인지를 어떻게 결정할지라든지를 보기 위해.
```yaml
logging:
  level:
    root: INFO
    org.springframework.cloud.gateway.route.
        RouteDefinitionRouteLocator: INFO
    org.springframework.cloud.gateway: TRACE
```

#### 라우팅 룰

라우팅 규칙 설정은 두 가지 방법으로 수행할 수 있다: 프로그래밍 방식으로 Java DSL을 사용하거나 Configuration을 통해 수행할 수 있다.
데이터베이스와 같은 외부 저장소에 규칙이 저장되어 있거나, 예를 들어 RESTful API 또는 게이트웨이로 보내는 메시지를 통해 런타임에 제공되는 경우, Java DSL을 사용하여 프로그래밍 방식으로 라우팅 규칙을 설정하는 것이 유용할 수 있다.
보다 정적인 사용 사례에서는, Configuration 파일인 src/main/resources/application.yml에 경로를 선언하는 것이 더 편리할 수 있다.
라우팅 규칙을 Java 코드에서 분리하면 마이크로서비스의 새 버전을 배포하지 않고도 라우팅 규칙을 업데이트할 수 있는 가능성이 생긴다.

라우팅은 다음과 같이 정의된다.

> - 들어오는 HTTP 요청의 정보를 기반으로 경로를 선택하는 **Predicates**
> - 요청 및/또는 응답을 수정할 수 있는 **Filters**
> - 요청을 보낼 위치를 설명하는 목적지 **URI**
> - 경로의 이름인 **ID**

#### product-composite 서비스로의 요청 라우팅 설정 예

```yaml
spring.cloud.gateway.routes:
  - id: product-composite
    uri: lb://product-composite
    predicates:
      - Path=/product-composite/**
```

상기 코드의 눈 여겨볼 포인트.

> - id: product-composite: 경로의 이름은 product-composite이다.
> - uri: lb://product-composite: 만약 이 경로가 그것의 predicates에 의해 선택되면, 요청은 디스커버리 서비스인 Netflix Eureka에서 product-composite라는 이름의 서비스로 라우팅된다. lb:// 프로토콜은 Spring Cloud Gateway가 클라이언트 측 로드 밸런서를 사용하여 디스커버리 서비스에서 목적지를 찾도록 지시하는 데 사용된다.
> - predicates: - Path=/product-composite/**는 이 경로가 어떤 요청과 일치해야 하는지 지정하는 데 사용된다. **는 경로에서 0개 이상의 요소와 매치된다. (모든 매치)

또한 SwaggerUI에 요청을 라우팅하기 위해서 product-composite에 추가 라우팅을 설정한다.

```yaml
- id: product-composite-swagger-ui
  uri: lb://product-composite
  predicates:
  - Path=/openapi/**
```

`/openapi/` 로 시작하는 모든 요청은 product-composite 서비스로 라우팅된다.


> - Swagger UI가 엣지 서버 뒤에 위치할 때, 이는 올바른 서버 URL을 포함하는 API의 OpenAPI 명세를 표시해야 한다. 즉, product-composite 서비스 자체의 URL이 아닌 엣지 서버의 URL을 표시해야 한다.  
    product-composite 서비스가 OpenAPI 명세에서 올바른 서버 URL을 생성할 수 있도록 하기 위해, 다음과 같은 구성이 product-composite 서비스에 추가되었다.
```yaml
server.forward-headers-strategy: framework
```

> - OpenAPI의 올바른 URL 명세가 설정되었는지 보기 위해 이하 테스트가 test-em-all.bash에 추가되었다.
```shell
  assertCurl 200 "curl -s  http://$HOST:$PORT/openapi/v3/api-docs"
  assertEqual "http://$HOST:$PORT" "$(echo $RESPONSE | jq -r .servers[].url)"
```

> - '/eureka/api/' 로 시작하는 모든 요청은 유레카 API로 라우팅 된다.
> - '/eureka/web/' 로 시작하는 모든 요청은 유레카 웹페이지로 라우팅 된다.

API 리퀘스트는 http://${app.eureka-server}:8761/eureka 로 라우팅 될 것이다. 유레카 API를 위한 라우팅 룰은 다음과 같다.
```yaml
- id: eureka-api
  uri: http://${app.eureka-server}:8761
  predicates:
  - Path=/eureka/api/{segment}
  filters:
  - SetPath=/eureka/{segment}
```

Path 값의 {segment} 부분은 경로에서 모든 요소와 일치하며, SetPath 값의 {segment} 부분을 대체하는 데 사용된다.
웹 페이지 요청은 http://${app.eureka-server}:8761로 라우팅된다. 웹 페이지는 .js, .css, .png 파일 등 여러 웹 리소스를 로드한다.
이러한 요청들은 http://${app.eureka-server}:8761/eureka로 라우팅된다. Eureka 웹 페이지에 대한 라우팅 규칙은 다음과 같다.
```yaml
- id: eureka-web-start
  uri: http://${app.eureka-server}:8761
  predicates:
  - Path=/eureka/web
  filters:
  - SetPath=/
- id: eureka-web-other
  uri: http://${app.eureka-server}:8761
  predicates:
  - Path=/eureka/**
```

${app.eureka-server} 값은 스프링 설정의 프로퍼티값에 따라 바뀌는 것이다. 로컬 환경에서는 localhost, 도커환경에서는 eureka이다.
```yaml
app.eureka-server: localhost
---
spring.config.activate.on-profile: docker
app.eureka-server: eureka
```

#### Predicates와 Filters를 이용한 요청 라우팅

http://httpstat.us/${CODE} 를 호출하면 간단하게 ${CODE}에 넣은 HTTP Status 응답을 리턴해준다. 테스트하기에 용이하다. 이후 테스트에 사용하기 위해 application.yml에 라우팅 규칙을 추가했다. 참고할 것.

> - i.feel.lucky 호출 시 return 200 OK
> - im.a.teapot  호출 시 return 418 I'm a teapot
> - 그 외 호스트네임 호출 시 return 501 Not Implemented

### 엣지 서버 테스트

1. 프로젝트 빌드
```shell
./gradlew clean build && docker-compose build
```

2. 스크립트 테스트
```shell
./test-em-all.bash start
```

3. 엣지서버만이 노출된 http://localhost:8080 을 통해 HTTP 통신을 하는지 확인할 것.

#### ***아래의 포인트들을 짚어보며 테스트를 한다.***

> - Docker 엔진에서 실행되는 시스템 랜드스케이프 외부에 엣지 서버가 무엇을 노출하는지 테스트한다.
> - 다음과 같은 가장 자주 사용되는 라우팅 규칙 중 일부를 테스트한다.
    >   - 엣지 서버를 통해 API를 호출하기 위한 URL 기반 라우팅 사용
>   - 엣지 서버를 통해 Swagger UI를 호출하기 위한 URL 기반 라우팅 사용
>   - API와 웹 기반 UI 모두를 사용하여 Netflix Eureka를 호출하기 위한 URL 기반의 라우팅 사용
>   - 요청의 호스트명에 따라 요청을 어떻게 라우팅할 수 있는지 보기 위해 헤더 기반의 라우팅 사용

#### 도커 엔진이 어떤것들을 외부로 노출시켰는지 확인

1. docker-compose ps 를 사용
```shell
docker-compose ps gateway eureka product-composite product recommendation review
```

2. 다음과 같은 결과를 볼 수 있는데 엣지 서버(Spring Cloud Gateway)만이 8080 포트를 도커엔진 외부로 노출시키고 있음을 알 수 있다.
   ![](./images/img_6.png)

3. 엣지서버에 셋팅된 라우팅 정보를 알고 싶다면 `/actuator/gateway/routes` 를 호출하면 된다. 필요한 정보만 보기 위해 jq 필터를 쓴다.
```shell
curl localhost:8080/actuator/gateway/routes -s | jq '.[] | {"\(.route_id)": "\(.uri)"}' | grep -v '{\|}'
```

### 라우팅 룰 테스트

#### 엣지 서버를 통한 product-composite API 호출

1. 엣지 서버에서 무슨 일이 일어나고 있는지 보기 위해 로그를 출력한다.
```shell
docker-compose logs -f --tail=0 gateway
```

2. 다른 창에서 product-composite API를 호출해본다.
```shell
curl http://localhost:8080/product-composite/1
```

3. 올바른 데이터가 출력되는 것을 확인한다.
4. 다음과 같은 로그를 확인한다.
```text
Pattern "/product-composite/**" matches against value "/product-composite/1"
Route matched: product-composite
...
LoadBalancerClientFilter url chosen: http://c46dfcbef421:8080/product-composite/1
```

5. 로그 출력에서 우리는 Configuration에서 지정한 predicate에 기반한 패턴 매칭을 볼 수 있고, 디스커버리 서버의 사용 가능한 인스턴스 중에서 엣지 서버가 어떤 마이크로서비스 인스턴스를 선택했는지 볼 수 있다.
   이 경우, 그것은 요청을 http://c46dfcbef421:8080/product-composite/1로 전달합니다. 이는 product-composite 서비스의 instanceId이자 Docker Container ID가 호스트네임이 되었다.

### Swagger UI 호출
http://localhost:8080/openapi/swagger-ui.html 로 접속하면 Swagger UI를 확인할 수 있다.


### 엣지 서버를 통한 유레카 서버 호출
1. 디스커버리 서버에 어떤 인스턴스들이 등록되었는지를 확인하기 위해 엣지서버를 통해 유레카 API를 호출한다.
```shell
curl -H "accept:application/json" \
localhost:8080/eureka/api/apps -s | \
jq -r .applications.application[].instance[].instanceId
```

2. 응답 확인

3. 유레카 웹 페이지 열어보기 http://localhost:8080/eureka/web


### 헤더 기반 라우팅

1. i.feel.lucky 호스트네임을 호출하기 위해 이하 코드 호출. (200 OK)
```shell
curl http://localhost:8080/headerrouting -H "Host: i.feel.lucky:8080"
```

2. im.a.teapot (418 I'm a teapot)
```shell
curl http://localhost:8080/headerrouting -H "Host: im.a.teapot:8080"
```

3. Host 헤더가 없을 시 (501 Not Implemented)
```shell
curl http://localhost:8080/headerrouting
```

### 테스트 종료
```shell
docker-compose down
```

## Securing Access to APIs

### OAuth2.0 : Authorization code grant flow

Resource owner: 최종 사용자.
Client: 최종 사용자를 대신하여 보호된 API를 호출하려는 제3의 클라이언트 애플리케이션, 예를 들어 웹 앱 또는 네이티브 모바일 앱.
Resource server: 우리가 보호하려는 API를 제공하는 서버. (내가 만든 어플리케이션 서버)
Authorization server: 인증 서버는 리소스 소유자, 즉 최종 사용자가 인증된 후에 클라이언트에게 토큰을 발급한다. 사용자 정보의 관리와 사용자의 인증은 일반적으로 뒷단에서 Identity Provider(IdP)에게 위임된다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_11_01.png)

1. 클라이언트 애플리케이션은 웹 브라우저에서 사용자를 인증 서버로 보내는 것으로 허가 흐름을 시작한다.
2. 인증 서버는 사용자를 인증하고 사용자의 동의를 요청한다.
3. 인증 서버는 인가 코드와 함께 사용자를 클라이언트 애플리케이션으로 다시 리디렉션한다. 인증 서버는 1단계에서 클라이언트가 지정한 리디렉션 URI를 사용하여 인가 코드를 어디로 보낼지 결정한다. 인가 코드는 웹 브라우저, 즉 잠재적으로 악성 JavaScript 코드가 인가 코드를 가져갈 수 있는 불안전한 환경을 통해 클라이언트 애플리케이션으로 전달되기 때문에 한 번만 사용할 수 있으며, 짧은 시간 동안만 사용할 수 있다.
4. 인가 코드를 액세스 토큰으로 교환하기 위해, 클라이언트 애플리케이션이 다시 인증 서버를 호출하도록 요구한다. 클라이언트 애플리케이션은 클라이언트 ID와 클라이언트 비밀번호, 그리고 인가 코드를 함께 제출해야 한다. 이 때, 클라이언트 비밀번호는 민감한 정보로 보호되어야 하므로 이 호출은 반드시 서버 쪽에서 실행되어야 한다.
5. 인증 서버는 액세스 토큰을 발급하고 이것을 클라이언트 어플리케에게 전송한다. 필요에 따라서, 인증 서버는 리프레시 토큰도 발행하고 반환할 수 있다.
6. 클라이언트는 액세스 토큰을 이용하여 리소스 서버에 의해 제공된 보호된 API에 요청을 보낼 수 있다.
7. 자원 서버는 액세스 토큰을 검사하고 검사에 성공하는 경우 요청을 처리한다. 6단계와 7단계는 액세스 토큰의 유효기간 동안 반복될 수 있다. 액세스 토큰의 수명이 만료되면, 클라이언트는 리프레시 토큰을 사용하여 새로운 접근 토큰을 얻을 수 있다.


### Securing the system landscape

> - 도청을 방지하기 위해 HTTPS를 사용하여 외부 API로부터의 요청과 응답을 암호화한다.
> - OAuth 2.0과 OpenID Connect를 사용하여 API에 접근하는 사용자와 클라이언트 애플리케이션을 인증하고 권한을 부여한다.
> - HTTP basic authentication을 사용하여 Netflix Eureka라는 디스커버리 서버에 대한 접근을 보호한다.

테스트 목적으로, 우리는 시스템 구성에 로컬 OAuth 2.0 인증 서버를 추가할 것이다. 인증 서버와의 모든 외부 통신은 엣지 서버를 통해 라우팅된다. 엣지 서버와 product-composite 서비스는 OAuth 2.0 리소스 서버로 작동하며, 즉 유효한 OAuth 2.0 액세스 토큰을 필요로 하여 접근을 허용한다.  
액세스 토큰 검증의 오버헤드를 최소화하기 위해, 우리는 이들이 signed JWTs로 인코딩되었다고 가정하고, 인증 서버가 리소스 서버가 사용할 수 있는 공개키(JSON Web Key Set 또는 줄여서 jwk-set, 서명을 검증하는데 필요)에 접근하는 endpoint를 제공한다고 가정한다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_11_02.png)

> - HTTPS는 외부 통신에 사용되며, 일반 텍스트 HTTP는 시스템 구성 내에서 사용된다.
> - 로컬 OAuth 2.0 인증 서버는 엣지 서버를 통해 외부에서 접근된다.
> - 엣지 서버와 product-composite 마이크로서비스 모두 액세스 토큰을 signed JWTs로 검증합니다.
> - 엣지 서버와 product-composite 마이크로서비스는 인증 서버의 jwk-set endpoint에서 공개키를 가져와 JWT 기반 액세스 토큰의 서명을 검증하는 데 사용합니다.

### Protecting external communication with HTTPS

#### 자기 서명 인증서 생성 (패스워드: password)
```shell
keytool -genkeypair -alias localhost -keyalg RSA -keysize 2048 -storetype PKCS12 -keystore edge.p12 -validity 3650
```

> - 소스코드 안에 인증서가 포함되어 있으므로 직접 생성할 필요 없음. gateway 프로젝트 내 src/main/resources/keystore/edge.p12 참고.
> - 이는 인증서가 빌드 타이밍에 .jar 파일 내에 위치한다는 것을 의미하며 런타임시엔 클래스패스 상 keystore/edge.p12 에 위치한다.

HTTPS와 인증서를 사용하기 위해 엣지서버를 설정한다. gateway 프로젝트의 application.yml 파일을 수정한다.
```yaml
server.port: 8443
server.ssl:
  key-store-type: PKCS12
  key-store: classpath:keystore/edge.p12
  key-store-password: password
  key-alias: localhost
```

#### 자가 서명 인증서 대체
jar 파일에 자체 서명된 인증서를 넣는 것은 개발에만 유용하다. 예를 들어 테스트나 운영 환경과 같은 런타임 환경에서 작동하는 솔루션을 위해서는, 인증된 CA(Certificate Authorities)가 서명한 인증서를 사용할 수 있어야 한다.  
또한 jar 파일을 다시 빌드할 필요 없이 런타임 동안 사용할 인증서를 지정할 수 있어야 하며, Docker를 사용할 때 jar 파일이 포함된 Docker 이미지도 마찬가지다.
Docker Compose를 사용하여 Docker 컨테이너를 관리하는 경우, Docker 컨테이너의 볼륨을 Docker 호스트에 있는 인증서로 매핑할 수 있다.
또한 Docker 컨테이너의 환경 변수를 설정하여 Docker 볼륨 내의 외부 인증서를 가리키게 할 수도 있다.


인증서 대체를 위해 다음을 따라한다.


1. 인증서를 하나 더 생성한다. 패스워드는 testtest로 설정한다.
```shell
mkdir keystore
keytool -genkeypair -alias localhost -keyalg RSA -keysize 2048 -storetype PKCS12 -keystore keystore/edge-test.p12 -validity 3650
```

2. docker-compose.yml 파일을 수정한다. 인증서의 패스, 인증서의 패스워드, 인증서 폴더가 맵핑 된 볼륨이 추가되었다.
```yaml
gateway:
  environment:
    - SPRING_PROFILES_ACTIVE=docker
    - SERVER_SSL_KEY_STORE=file:/keystore/edge-test.p12
    - SERVER_SSL_KEY_STORE_PASSWORD=testtest
  volumes:
    - $PWD/keystore:/keystore
  build: spring-cloud/gateway
  mem_limit: 512m
  ports:
    - "8443:8443"
```

3. 엣지서버가 기동 중이라면, 다음 커맨드로 재시동이 필요하다.
```shell
docker-compose up -d --scale gateway=0
docker-compose up -d --scale gateway=1
```

### Securing access to the discovery server

#### 유레카 서버 설정

1. build.gradle
```yaml
implementation 'org.springframework.boot:spring-boot-starter-security'
```

2. SecurityConfig 클래스 추가
- 유저 정의
```java
@Bean
public InMemoryUserDetailsManager userDetailsService() {
  UserDetails user = User.withDefaultPasswordEncoder()
      .username(username)
      .password(password)
      .roles("USER")
      .build();
  return new InMemoryUserDetailsManager(user);
}
```

- username, password 주입
```java
@Autowired
public SecurityConfig(
  @Value("${app.eureka-username}") String username,
  @Value("${app.eureka-password}") String password
) {
  this.username = username;
  this.password = password;
}
```

- 모든 API 및 웹페이지가 HTTP basic authentication으로 보호된다.
```java
@Bean
public SecurityFilterChain configure(HttpSecurity http) throws Exception {
  http
    // Disable CRCF to allow services to register themselves with Eureka
    .csrf()
      .disable()
    .authorizeRequests()
      .anyRequest().authenticated()
      .and()
      .httpBasic();
  return http.build();
}
```

3. 계정정보 추가 (application.yml)
```yaml
app:
 eureka-username: u
 eureka-password: p
```

4. 마지막으로 EurekaServerApplicationTests 에서는 설정 파일에 저장된 계정 정보를 이용해 유레카 서버를 테스트 한다.
```java
@Value("${app.eureka-username}")
private String username;
 
@Value("${app.eureka-password}")
private String password;
 
@Autowired
public void setTestRestTemplate(TestRestTemplate testRestTemplate) {
   this.testRestTemplate = testRestTemplate.withBasicAuth(username, password);
}
```

상기 설정은 디스커버리 서버(유레카)에 접근을 제한하기 위해 필요하다. 이제부턴 HTTP basic authentication을 사용하며 접근하기 위해서는 username과 password가 필요하다.
마지막 설정은 디스커버리 서버에 접근하기 위해 유레카 클라이언트에 인증 정보를 설정하는 것이다.

#### 유레카 클라이언트 설정

application.yml 에 설정한다.
```yaml
app:
  eureka-username: u
  eureka-password: p
 
eureka:
  client:
     serviceUrl:
       defaultZone: "http://${app.eureka-username}:${app.eureka-password}@${app.eureka-server}:8761/eureka/"
```

### Adding a local authorization server 로컬 인증서버 추가

OAuth 2.0과 OpenID Connect를 사용하여 보안된 API와 함께 로컬에서 테스트를 완전히 자동화하여 실행하기 위해, 이러한 사양에 준수하는 인증 서버를 시스템 구성에 추가할 것이다.
Spring Security는 기본적으로 인증 서버를 제공하지 않았지만 2020년 4월, Spring Security 팀이 주도하는 커뮤니티 주도 프로젝트인 Spring Authorization Server가 인증 서버를 제공하는 목표로 밮표되었다.
2021년 8월에는 Spring Authorization Server 프로젝트가 실험 상태에서 벗어나 Spring 프로젝트 포트폴리오의 멤버가 되었다.


Spring Authorization Server는 OpenID Connect 디스커버리 엔드포인트의 사용과 액세스 토큰의 디지털 서명을 모두 지원한다.
또한 디스커버리 정보를 사용하여 토큰의 디지털 서명을 검증하는 데 필요한 키를 얻기 위해 접근할 수 있는 엔드포인트도 제공한다.
이러한 기능들을 지원함으로써, 시스템 구성이 예상대로 작동하는지 검증하는 로컬 및 자동화된 테스트에서 인증 서버로 사용될 수 있다.

see. https://github.com/spring-projects/spring-authorization-server/tree/main/samples/default-authorizationserver

> - OpenID Connect 규격에 따른 가장 중요한 엔드포인트에 대한 접근을 검증하는 단위 테스트가 추가되었다.
> - 단일 등록 사용자의 사용자 이름과 비밀번호는 각각 u와 p로 설정된다.
> - 두 개의 OAuth 클라이언트, reader와 writer가 등록되어 있다. reader 클라이언트에는 product:read 스코프가 부여되고, writer 클라이언트에는 product:read와 product:write 스코프 둘 다 부여된다. 클라이언트들은 각각 secret-reader와 secret-writer로 클라이언트 시크릿을 설정하도록 구성된다.
> - 클라이언트들의 허용된 리다이렉션 URI들은 https://my.redirect.uri 와 https://localhost:8443/webjars/swagger-ui/oauth2-redirect.html 로 설정된다. 첫 번째 URI는 아래에서 설명할 테스트에서 사용되며, 두 번째 URI는 Swagger UI 컴포넌트에서 사용될 것이다.
> - 보안상의 이유로 인증 서버는 기본적으로 https://localhost 로 시작하는 리다이렉션 URI를 허용하지 않는다.

그러나 테스트 목적으로 인증서버는 https://localhost 를 허용하였다.  
see. https://docs.spring.io/spring-authorization-server/docs/1.0.0/reference/html/protocol-endpoints.html#oauth2-authorization-endpoint-customizing-authorization-request-validation


시스템 랜드스케이프에 맞춰서 다음 요소가 수정 및 적용되었다.

> - 공통 빌드 파일인 settings.gradle에 서버가 추가됨.
> - docker-compose.yml에 서버가 추가됨.
> - 인증 서버 헬스체크를 위해 HealthCheckConfiguration 클래스 추가 (gateway)
> - application.yml에 인증 서버에 대한 라우팅 규칙 추가 (/oauth, /login, /error). 이들 URI는 클라이언트에게 토큰을 발급, 사용자 인증, 에러 메시지를 표시하기 위해 사용된다. (gateway)
> - 이들 세 개의 URI는 엣지 서버에 의해 보호되지 않아야 하므로, SecurityConfig 클래스에 이들 URI에 대한 모든 요청을 허용하는 설정을 추가한다. (gateway)

### Protecting APIs using OAuth 2.0 and OpenID Connect

인증 서버가 준비되면, 엣지 서버와 product-composite 서비스를 OAuth 2.0 리소스 서버로 강화하여 유효한 액세스 토큰이 있어야만 접근을 허용하도록 만들 수 있다.
엣지 서버는 인증 서버에서 제공하는 디지털 서명을 사용하여 검증할 수 있는 모든 액세스 토큰을 받아들일 것으로 설정할 것이다.
또한 product-composite 서비스는 액세스 토큰이 유효한 OAuth 2.0 스코프를 포함하도록 요구할 것이다.

> - product:read 스코프는 product-composite 서비스의 read-only API를 호출하기 위해 사용된다.
> - product:write 스코프는 product-composite 서비스의 create 및 delete API를 호출하기 위해 사용된다.

product-composite 서비스는 또한 Swagger UI 컴포넌트가 인증 서버와 상호작용하여 액세스 토큰을 발급할 수 있도록 하는 설정으로 강화될 것입니다.
이렇게 하면 Swagger UI 웹 페이지의 사용자들이 보호된 API를 테스트할 수 있습니다.

### 엣지 서버, product-composite 서비스 변경

- OAuth2.0을 지원하기 위해 리소스 서버(gateway, product-composite)에 build.gradle 에 의존성 추가
```yaml
implementation 'org.springframework.boot:spring-boot-starter-security'
implementation 'org.springframework.security:spring-security-oauth2-resource-server'
implementation 'org.springframework.security:spring-security-oauth2-jose'
```

- 양쪽에 SecurityConfig 클래스 추가
```java
@Configuration
@EnableWebFluxSecurity
public class SecurityConfig {
 
  @Bean
  SecurityWebFilterChain springSecurityFilterChain(ServerHttpSecurity http) {
    http
      .authorizeExchange()
        .pathMatchers("/actuator/**").permitAll()
        .anyExchange().authenticated()
        .and()
      .oauth2ResourceServer()
        .jwt();
    return http.build();
  }
}
```

> - @EnableWebFluxSecurity 어노테이션은 Spring WebFlux 기반의 API에 대한 Spring Security 지원을 활성화한다.
> - .pathMatchers("/actuator/**").permitAll()는 보호되지 않아야 하는 URL에 대해 제한 없는 접근을 허용하는 데 사용된다. actuator 엔드포인트는 프로덕션으로 넘어가기 전에 보호되어야 합니다.
> - .anyExchange().authenticated()은 모든 다른 URL로의 접근을 허용하기 전에 사용자가 인증받았음을 확인한다.
> - .oauth2ResourceServer().jwt()은 인증이 JWT로 인코딩된 OAuth 2.0 액세스 토큰을 기반으로 할 것임을 지정한다.


> - 시스템 랜드스케이프가 시작되면 디스커버리 엔드포인트를 테스트할 수 있다. 예를 들어, 토큰의 디지털 서명을 검증하는 데 필요한 키를 반환하는 엔드포인트를 찾기 위해 이 명령을 사용할 수 있다.  
    ```docker-compose exec auth-server curl localhost:9999/.well-known/openid-configuration -s | jq -r .jwks_uri```


### product-composite 서비스만의 변경점

> - SecurityConfig 클래스의 보안 구성은 액세스 토큰에 OAuth 2.0 스코프를 요구함으로써 액세스를 허용하도록 개선되었다.

```java
.pathMatchers(POST, "/product-composite/**")
  .hasAuthority("SCOPE_product:write")
.pathMatchers(DELETE, "/product-composite/**")
  .hasAuthority("SCOPE_product:write")
.pathMatchers(GET, "/product-composite/**")
  .hasAuthority("SCOPE_product:read")
```

> - 컨벤션에 따라 Spring Security를 사용하여 권한을 확인할 때 OAuth 2.0 스코프는 SCOPE_로 접두사를 붙여야 한다.

> - JWT로 인코딩된 액세스 토큰으로부터 관련 부분을 기록하기 위해 logAuthorizationInfo() 메소드가 API 호출 시마다 추가되었다. 액세스 토큰은 표준 Spring Security SecurityContext를 사용하여 얻을 수 있으며, 리액티브 환경에서는 정적 도우미 메소드인 ReactiveSecurityContextHolder.getContext()를 사용하여 얻을 수 있다. 자세한 내용은 ProductCompositeServiceImpl 클래스를 참조.
> - Spring 기반 통합 테스트를 실행할 때 OAuth 사용이 비활성화되었습니다. 통합 테스트를 실행할 때 OAuth 메커니즘이 작동하지 않도록 하기 위해 다음과 같이 비활성화합니다:
    >   - 테스트 중에 사용할 TestSecurityConfig라는 보안 구성이 추가되었습니다. 이 구성은 모든 리소스에 대한 액세스를 허용합니다.
```java
http.csrf().disable().authorizeExchange().anyExchange().permitAll();
```
> - - 각 Spring 통합 테스트 클래스에서는 다음과 같이 기존의 보안 구성을 무시하고 TestSecurityConfig를 구성한다.

```java
@SpringBootTest( 
  classes = {TestSecurityConfig.class},
  properties = {"spring.main.allow-bean-definition-overriding=true"})
```

#### Swagger UI가 액세스 토큰을 얻을 수 있도록 변경

> - product-composite 서비스의 SecurityConfig에 추가
```java
.pathMatchers("/openapi/**").permitAll()
.pathMatchers("/webjars/**").permitAll()
```
> - 시큐리티 스키마 ```security_auth```를 얻기 위해서 OpenAPI 스펙이 더해졌다. (OpenApiConfig.java 참조)

API 서비스의 ProductCompositeService에 다음이 추가되었다.
```java
@SecurityRequirement(name = "security_auth")
```

> - 보안 스키마 security_auth의 의미를 정의하기 위해 product-composite 프로젝트에 OpenApiConfig 클래스가 추가되었다.
```java
@SecurityScheme(
  name = "security_auth", type = SecuritySchemeType.OAUTH2,
  flows = @OAuthFlows(
    authorizationCode = @OAuthFlow(
      authorizationUrl = "${springdoc.oAuthFlow.authorizationUrl}",
      tokenUrl = "${springdoc.oAuthFlow.tokenUrl}", 
      scopes = {
        @OAuthScope(name = "product:read", description = "read scope"),
        @OAuthScope(name = "product:write", description = "write scope")
      }
)))
public class OpenApiConfig {}
```

위의 코드를 통해 알 수 있는 것은

> - 보안 스키마는 OAuth 2.0을 기반으로 한다.
> - 인가 코드 승인 흐름(grant flow)이 사용될 것이다.
> - 인가 코드와 액세스 토큰을 얻기 위한 필요한 URL은 springdoc.oAuthFlow.authorizationUrl 및 springdoc.oAuthFlow.tokenUrl 매개변수를 사용하여 구성에서 제공될 것이다.
> - Swagger UI가 API를 호출할 수 있도록 필요한 스코프 목록은 ```product:read``` 및 ```product:write```이다.

마지막으로 application.yml에 추가된 몇몇 설정
```yaml
  swagger-ui:
    oauth2-redirect-url: /swagger-ui/oauth2-redirect.html
    oauth:
      clientId: writer
      clientSecret: secret-writer
      useBasicAuthenticationWithAccessCodeGrant: true
  oAuthFlow:
    authorizationUrl: https://localhost:8443/oauth2/authorize
    tokenUrl: https://localhost:8443/oauth2/token
```

위 설정을 통해 알 수 있는 것은
> - Swagger UI가 인증 코드를 얻기 위해 사용할 리디렉션 URL.
> - client id와 client secret.
> - 인증 서버에 대해 자신을 식별할 때 HTTP 기본 인증을 사용.
> - 앞에서 설명한 OpenApiConfig 클래스에서 사용되는 authorizationUrl 및 tokenUrl 매개변수의 값. 이 URL은 웹 브라우저에서 사용되며 product-composite 서비스 자체에서 사용되지 않는다.

위 설정들을 함으로써 엣지 서버와 product-composite 서비스가 OAuth 2.0 리소스 서버로 동작할 수 있게 되었다.  
또한 Swagger UI 컴포넌트는 OAuth 2.0 클라이언트로 동작한다. OAuth 2.0 및 OpenID Connect의 사용법을 소개하기 위해 수행해야 할 마지막 단계는 테스트 스크립트를 업데이트하여 액세스 토큰을 획득하고 테스트 실행 시 사용해보는 것이다.

### 테스트 스크립트 업데이트

먼저, 우리는 헬스체크 API를 제외한 모든 API를 호출하기 전에 액세스 토큰을 얻어야 한다. 생성 및 삭제 API를 호출할 수 있도록 `wrtier` 클라이언트로 액세스 토큰을 얻는다.

```shell
ACCESS_TOKEN=$(curl -k https://writer:secret-writer@$HOST:$PORT/oauth2/token -d grant_type=client_credentials -d scope="product:read product:write" -s | jq .access_token -r)
```

위 커맨드에서는 HTTP Basic Authentication을 사용하고 있으며, hostname 앞에 writer:secret-writer@로 client id와 client secret을 전달하고 있다.  
스코프 기반의 인증을 테스트하기 위해 두 개의 테스트 케이스가 스크립트에 추가되었다.

```shell
# Verify that a request without access token fails on 401, Unauthorized
assertCurl 401 "curl -k https://$HOST:$PORT/product-composite/$PROD_ID_REVS_RECS -s"
# Verify that the reader client with only read scope can call the read API but not delete API
READER_ACCESS_TOKEN=$(curl -k https://reader:secret-reader@$HOST:$PORT/oauth2/token -d grant_type=client_credentials -d scope="product:read" -s | jq .access_token -r)
READER_AUTH="-H \"Authorization: Bearer $READER_ACCESS_TOKEN\""
assertCurl 200 "curl -k https://$HOST:$PORT/product-composite/$PROD_ID_REVS_RECS $READER_AUTH -s"
assertCurl 403 "curl -k https://$HOST:$PORT/product-composite/$PROD_ID_REVS_RECS $READER_AUTH -X DELETE -s"
```

> - 첫 번째 테스트는 액세스 토큰을 제공하지 않고 API를 호출한다. API는 401 Unauthorized HTTP 상태를 반환하는 것으로 예상한다.
> - 두 번째 테스트는 reader 클라이언트가 읽기 전용 API를 호출할 수 있는지 확인한다.
> - 마지막 테스트는 읽기 권한만 부여된 reader 클라이언트를 사용하여 업데이트 API를 호출한다. 삭제 API로 보내는 요청은 403 Forbidden HTTP 상태를 반환하는 것으로 예상한다.


### 로컬 인증 서버를 통한 테스트

1. 먼저, 소스에서 빌드하고 테스트 스크립트를 실행하여 모든 것이 함께 작동하는지 확인한다.
2. 다음으로, 우리는 보호된 검색 서버의 API와 웹 페이지를 테스트한다.
3. 그 후에, OAuth 2.0 클라이언트 자격 증명 및 승인 코드 그랜트 플로우를 사용하여 액세스 토큰을 얻는 방법을 배우게 된다.
4. 발급된 액세스 토큰을 사용하여 보호된 API를 테스트할 것이다. 또한, reader 클라이언트에 대해 발급된 액세스 토큰을 사용하여 업데이트 API를 호출할 수 있는지도 확인할 것입니다. (이는 실패해야 한다.)
5. 마지막으로, Swagger UI가 액세스 토큰을 발급하고 API를 호출할 수 있는지도 확인할 것이다.

```shell
./gradlew build && docker-compose build
./test-em-all.bash start
```

### 보호된 디스커버리 서버(유레카) 테스트

Eureka라는 보호된 디시커버리 서버가 실행 중인 경우, 해당 API 및 웹 페이지에 액세스 할 수 있도록 유효한 자격 증명을 제공해야 한다.
예를 들어, 등록된 인스턴스를 Eureka 서버에 요청하는 것은 다음과 같은 curl 명령을 통해 수행 할 수 있다. 여기서는 URL에 직접 사용자 이름과 비밀번호를 제공한다.

```shell
curl -H "accept:application/json" https://u:p@localhost:8443/eureka/api/apps -ks | jq -r .applications.application[].instance[].instanceId
```

웹페이지 `https://localhost:8443/eureka/web` 를 통해 직접 접속하는 경우 브라우저가 사용자 이름과 비밀번호를 묻는데 username=u, password=p를 직접 입력한다.

### 액세스 토큰 획득

#### 클라이언트 자격 증명 흐름을 통해 액세스 토큰 획득

`writer` 클라이언트, 즉, `product:reader`와 `product:writer` 스코프를 모두 가진 액세스 토큰을 얻는다.

```shell
curl -k https://writer:secret-writer@localhost:8443/oauth2/token -d grant_type=client_credentials -d scope="product:read product:write" -s | jq .
```

```shell
{
  "access_token": "eyJraWQiOiI...",
  "scope": "product:write product:read",
  "token_type": "Bearer",
  "expires_in": 3599
}
```

> - 액세스 토큰 자체.
> - 토큰에 부여된 스코프. writer 클라이언트는 product:write 및 product:read 스코프 모두를 부여받았다. 또한 openid 스코프도 부여받았으며, 이는 사용자의 ID에 관한 정보에 액세스할 수 있도록 해준다. 예를 들어 이메일 주소와 같은 정보.
> - 받은 토큰의 유형. Bearer는 이 토큰의 소유자가 토큰에 부여된 스코프에 따라 액세스를 받아야 함을 의미한다.
> - 액세스 토큰의 유효한 시간(이 경우 3599초)이다.

#### Authorization Code Grant Flow(인가 코드 그랜트 플로우)를 통한 액세스 토큰 획득
인증 코드 그랜트 플로우를 사용하여 액세스 토큰을 얻으려면 웹 브라우저를 사용해야한다. 이 그랜트 플로우는 `부분적으로 보안되지 않은 환경(웹 브라우저 안)`에서도 안전을 보장하기 위해 약간 복잡해진다.
첫 번째 보안되지 않은 단계에서는 웹 브라우저를 사용하여 액세스 토큰으로 교환할 수 있는 단 한 번만 사용할 수 있는 인증 코드를 얻는다.
인증 코드는 웹 브라우저에서 안전한 레이어 (예: 서버 측 코드)로 전달되어 인증 서버에 대한 새로운 요청을 생성하여 인증 코드를 액세스 토큰으로 교환한다.
이 안전한 교환에서 서버는 신원을 확인하기 위해 client secret을 제공해야 한다. 다음 단계를 수행하여 인증 코드 그랜트 플로우를 실행하라.

1. reader 클라이언트를 위한 인가 코드를 얻기 위해 다음 URL을 실행한다.  
   `https://localhost:8443/oauth2/authorize?response_type=code&client_id=reader&redirect_uri=https://my.redirect.uri&scope=product:read&state=35725`
2. 접속을 위한 username u, password p를 입력한다.
3. reader 클라이언트 승낙을 요구받게 되면 체크 후 승낙한다.
4. `Submit Consent` 버튼을 누르면 `ERR_NAME_NOT_RESOLVED` 화면을 보게 된다.
5. 처음 보면 조금 실망스러울 수도 있다. 인증 서버가 웹 브라우저로 다시 보낸 URL은 초기 요청에서 클라이언트가 지정한 리다이렉트 URI를 기반으로 한다. URL을 텍스트 편집기로 복사하면 다음과 비슷한 내용을 찾을 수 있다.  
   `https://my.redirect.uri/?code=7XBs...0mmyk&state=35725`  
   리다이렉트 URL 중 code 요청 매개변수에서 인증 코드를 찾을 수 있다. code 매개변수에서 인증 코드를 추출하고 그 값을 가진 `환경 변수(Environment Variable)` CODE를 정의하라.  
   `CODE=7XBs...0mmyk`
6. 다음 curl 명령을 사용하여 인증 코드를 액세스 토큰으로 교환하는 백엔드 서버인 척 해보자.
```shell
curl -k https://reader:secret-reader@localhost:8443/oauth2/token \
 -d grant_type=authorization_code \
 -d client_id=reader \
 -d redirect_uri=https://my.redirect.uri \
 -d code=$CODE -s | jq .
```
응답:
```shell
{
  "access_token": "eyJraWQiOiIyYTNlYjI5MS0xZjg4LTQyMjUtYTI2NC1iNzZiNDVkYjI5NTkiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ1IiwiYXVkIjoicmVhZGVyIiwibmJmIjoxNjk1MTk0MjcwLCJzY29wZSI6WyJwcm9kdWN0OnJlYWQiXSwiaXNzIjoiaHR0cDovL2F1dGgtc2VydmVyOjk5OTkiLCJleHAiOjE2OTUxOTc4NzAsImlhdCI6MTY5NTE5NDI3MH0.fsK9HrHMOreDx1XqVj8QTfruLW3Vh0ZngGW6UFhanG6dOKDHWh4gtCy33SnfVGsphRGqJZRuqwIi7TNxKEmI0mldiK9VytwW-5K6Okg5chEBfPeF2dLLm7uh-fo7imv6SkzrpCJUqotzwevGr2qS97bOwoKWwXBTBOVtsV20QDrm7TfEpkMUGOBnPZid3D6kh6of3bUsoPcQ9HJzdQD-_PdO4TRHUsNjin637goZkq1g8WqVf3E7o-2MRqYSLl9FIHqHTMNwUTe-v2Z4GNSk7Onq3n4DWTzPo4oM9dFgoI2nhIbJ1yz-_20Mg5U5jrzDr4SfiRncK70K04LPaOuSDw",
  "refresh_token": "9UAjCbXNW_myJ5YRRcSwakTyAAkgBCe4RdJ1TaN_PWl1mBW3pDgIHnITkdy1DmSf40eUIQu3cPiVZdYpUR2DbCr-4NGre_h7Mw6csVakBvI2J9EOu3yFqY95vsH2dk0x",
  "scope": "product:read",
  "token_type": "Bearer",
  "expires_in": 3599
}
```

위에서 볼 수 있듯이, 클라이언트 자격 증명 흐름에서 받은 것과 유사한 정보를 응답에서 얻었지만, 다음과 같은 다른점이 있다.

> - 더 안전한 권한 흐름을 사용했기 때문에, 리프레시 토큰도 발급되었다.
> - reader 클라이언트를 위한 액세스 토큰을 요청했기 때문에, product:write 스코프 없이 product:read 스코프만 얻었다,

### 액세스 토큰을 사용하여 보호된 API 호출

1. 액세스 토큰 없이 product-composite API 호출해보기
```shell
ACCESS_TOKEN=an-invalid-token
curl https://localhost:8443/product-composite/1 -k -H "Authorization: Bearer $ACCESS_TOKEN" -i
```
응답:
```shell
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer error="invalid_token", error_description="An error occurred while attempting to decode the Jwt: Invalid JWT serialization: Missing dot delimiter(s)", error_uri="https://tools.ietf.org/html/rfc6750#section-3.1"
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000 ; includeSubDomains
X-Frame-Options: DENY
X-XSS-Protection: 0
Referrer-Policy: no-referrer
content-length: 0
```

2. reader 클라이언트를 위해 이전 단계에서 획득했던 액세스 토큰을 넣어 테스트 해보기
```shell
ACCESS_TOKEN=eyJraWQiOiIyYTNlYjI5MS0xZjg4LTQyMjUtYTI2NC1iNzZiNDVkYjI5NTkiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ1IiwiYXVkIjoicmVhZGVyIiwibmJmIjoxNjk1MTk0MjcwLCJzY29wZSI6WyJwcm9kdWN0OnJlYWQiXSwiaXNzIjoiaHR0cDovL2F1dGgtc2VydmVyOjk5OTkiLCJleHAiOjE2OTUxOTc4NzAsImlhdCI6MTY5NTE5NDI3MH0.fsK9HrHMOreDx1XqVj8QTfruLW3Vh0ZngGW6UFhanG6dOKDHWh4gtCy33SnfVGsphRGqJZRuqwIi7TNxKEmI0mldiK9VytwW-5K6Okg5chEBfPeF2dLLm7uh-fo7imv6SkzrpCJUqotzwevGr2qS97bOwoKWwXBTBOVtsV20QDrm7TfEpkMUGOBnPZid3D6kh6of3bUsoPcQ9HJzdQD-_PdO4TRHUsNjin637goZkq1g8WqVf3E7o-2MRqYSLl9FIHqHTMNwUTe-v2Z4GNSk7Onq3n4DWTzPo4oM9dFgoI2nhIbJ1yz-_20Mg5U5jrzDr4SfiRncK70K04LPaOuSDw
curl https://localhost:8443/product-composite/1 -k -H "Authorization: Bearer $ACCESS_TOKEN" -i 
```
응답:
```shell
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 714
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000 ; includeSubDomains
X-Frame-Options: DENY
X-XSS-Protection: 0
Referrer-Policy: no-referrer

{"productId":1,"name":"product name C","weight":300,"recommendations":[{"recommendationId":1,"author":"author 1","rate":1,"content":"content 1"},{"recommendationId":2,"author":"author 2","rate":2,"content":"content 2"},{"recommendationId":3,"author":"author 3","rate":3,"content":"content 3"}],"reviews":[{"reviewId":1,"author":"author 1","subject":"subject 1","content":"content 1"},{"reviewId":2,"author":"author 2","subject":"subject 2","content":"content 2"},{"reviewId":3,"author":"author 3","subject":"subject 3","content":"content 3"}],"serviceAddresses":{"cmp":"1ef929870e66/172.19.0.9:8080","pro":"9b1784fffa93/172.19.0.10:8080","rev":"53d0ef15f347/172.19.0.11:8080","rec":"3ade6fff7278/172.19.0.8:8080"}}
```

3. 만일 읽기가 아닌 수정 API를 호출하면 실패할 것이다.
```shell
ACCESS_TOKEN=eyJraWQiOiIyYTNlYjI5MS0xZjg4LTQyMjUtYTI2NC1iNzZiNDVkYjI5NTkiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ1IiwiYXVkIjoicmVhZGVyIiwibmJmIjoxNjk1MTk0MjcwLCJzY29wZSI6WyJwcm9kdWN0OnJlYWQiXSwiaXNzIjoiaHR0cDovL2F1dGgtc2VydmVyOjk5OTkiLCJleHAiOjE2OTUxOTc4NzAsImlhdCI6MTY5NTE5NDI3MH0.fsK9HrHMOreDx1XqVj8QTfruLW3Vh0ZngGW6UFhanG6dOKDHWh4gtCy33SnfVGsphRGqJZRuqwIi7TNxKEmI0mldiK9VytwW-5K6Okg5chEBfPeF2dLLm7uh-fo7imv6SkzrpCJUqotzwevGr2qS97bOwoKWwXBTBOVtsV20QDrm7TfEpkMUGOBnPZid3D6kh6of3bUsoPcQ9HJzdQD-_PdO4TRHUsNjin637goZkq1g8WqVf3E7o-2MRqYSLl9FIHqHTMNwUTe-v2Z4GNSk7Onq3n4DWTzPo4oM9dFgoI2nhIbJ1yz-_20Mg5U5jrzDr4SfiRncK70K04LPaOuSDw
curl https://localhost:8443/product-composite/999 -k -H "Authorization: Bearer $ACCESS_TOKEN" -X DELETE -i
```
응답:
```shell
HTTP/1.1 403 Forbidden
WWW-Authenticate: Bearer error="insufficient_scope", error_description="The request requires higher privileges than provided by the access token.", error_uri="https://tools.ietf.org/html/rfc6750#section-3.1"
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000 ; includeSubDomains
X-Frame-Options: DENY
X-XSS-Protection: 0
Referrer-Policy: no-referrer
content-length: 0
```

4. writer 클라이언트로써 획득한 액세스 토큰으로 3의 시도를 한다면 성공화 함께 202 응답을 얻을 것이다.

> 삭제 연산은 멱등성을 가지므로(요소가 있으면 제거하고, 요소가 없으면 제거하지 않는다. 즉, 여러번 실행해도 동일한 효과가 있으므로 멱등성이 성립함), 지정된 제품 ID의 제품이 기본 데이터베이스에 존재하지 않더라도 삭제 연산은 202를 반환해야 한다.


### AOuth 2.0을 이용한 Swagger UI 테스트

1. 웹 브라우저에서 `https://localhost:8443/openapi/swagger-ui.html` 접속
2. 시작 페이지에서 새로운 버튼 `Authorize` 버튼이 추가되었다. 인가 코드 그랜트 플로우를 시작하기 위해 클릭한다.
3. Swagger UI는 권한 서버에 액세스 요청할 스코프 목록을 보여준다. 'select all'이라는 텍스트의 링크를 클릭하여 모든 스코프를 선택하고, 그 다음 'Authorize' 버튼을 클릭.

![](./images/img_7.png)

4. 인증 서버는 사용자 이름과 비밀번호를 요구한다. username u, password p를 입력하고 'Sign in' 버튼을 클릭한다.
5. Consent required 페이지에서는 모든 권한을 선택 후 'Submit Consent' 버튼 클릭.

![](./images/img_8.png)

6. Swagger UI는 모든 인가 과정을 끝내고 그랜트 플로우 결과 창을 보여준다. Close 버튼을 누르고 시작 페이지로 돌아간다.
7. API 테스트를 시작할 수 있다.

### 외부 OpenID Connect 공급자를 사용한 테스트 (Testing with an external OpenID Connect provider)

직접 제어하는 인증 서버와 OAuth는 잘 작동하는 것을 확인했다. 하지만 이를 인증된 OpenID Connect 제공자로 교체하면 어떻게 될까? 이론적으로는 별도 설정없이 바로(out of the box) 작동해야 합니다. 확인해 보자.

> 인증된 OIDC 기관 목록을 보려면 참조 - https://openid.net/developers/certified/

외부 OpenID 제공자로의 테스트를 위해 Auth0, https://auth0.com/ 를 사용할 것이다. 자체 인증 서버 대신 Auth0를 사용할 수 있도록, 다음 사항을 체크해보자.

> - Auth0에서 리더 및 라이터 클라이언트와 사용자 계정 설정하기
> - OpenID 제공자로 Auth0를 사용하도록 필요한 변경사항 적용하기
> - 작동하는지 확인하기 위해 테스트 스크립트 실행하기
> - 다음의 인증 흐름을 이용해 액세스 토큰 획득하기:
    >   - 클라이언트 자격 증명 인증 흐름
    >   - 인가 코드 인증 흐름
> - 인증 흐름에서 얻은 액세스 토큰을 이용하여 보호된 API 호출하기
> - 사용자 정보 엔드포인트를 사용하여 사용자에 대한 더 많은 정보 얻기


### OAuth0를 위한 설정

Auth0에서 필요한 대부분의 설정은 Auth0의 관리 API를 사용하는 스크립트에 의해 처리된다. 하지만 관리 API에 접근할 수 있는 클라이언트 ID와 클라이언트 비밀번호를 Auth0가 생성할 때까지 몇 가지 작업을 해야한다.
Auth0의 서비스는 멀티 테넌트로, 우리가 클라이언트, 리소스 소유자, 리소스 서버와 같은 OAuth 객체의 도메인을 생성할 수 있게 한다.
Auth0에서 무료 계정에 가입하고 관리 API에 접근할 수 있는 클라이언트를 생성하기 위해 다음의 작업을 수행하라.

1. `https://auth0.com` 접속 후 회원가입
- 가입 후, 테넌트 도메인을 생성하라는 요청이 나온다. 선택한 테넌트의 이름을 입력한다. 예를 들어: dev-ml-3rd.eu.auth0.com.
- 요청된대로 계정 정보를 작성.
- 또한, 'Please Verify Your Auth0 Account'라는 제목의 이메일이 메일함에 있으면 그 안의 지시사항을 따라 계정을 인증.
2. 가입 후, 온보딩 페이지로 이동된다.
3. 왼쪽 메뉴에서 'Applications'를 클릭하여 확장하고, 관리 API인 'Auth0 Management API'를 찾기 위해 'APIs'를 클릭. 이 API는 테넌트 생성 중에 자동으로 만들어진다. 이 API를 사용하여 테넌트 내 필요한 정의들을 생성할 것이다.
4. Auth0 Management API를 클릭하고 Test 탭을 선택.
5. 'Create & Authorize Test'라는 글자가 크게 적힌 버튼이 표시된다. 관리 API에 접근할 수 있는 클라이언트 생성을 위해 이 버튼을 클릭.
6. 생성되면, 'Asking Auth0 for tokens from my application.'이란 제목의 페이지가 표시된다. 마지막 단계로서, 우리는 생성된 클라이언트에게 관리 APIs 사용 권한을 부여해야 한다.
7. Test 탭 옆에 있는 Machine to Machine Applications 탭을 클릭.
8. 여기서 우리는 테스트 클라이언트인 Auth0 Management API (Test Application)를 찾아볼 수 있고, 그것에 관리 API 사용 권한이 부여된 것으로 표시된다. Authorized toggle button 옆의 아래 화살표를 누르면 많은 수의 사용 가능한 권한들이 나타난다.
9. All 선택 사항을 누르고 Update 버튼도 누른다. 테넌트 내 모든 관리 API에 접근 권한을 가진 매우 강력한 클라이언트를 이제 보유하게 되었음을 이해한 후 'Continue' 버튼을 클릭.
10. 이제 생성된 클라이언트의 클라이언트 ID와 클라이언트 비밀번호를 수집하기만 하면 된다. 왼쪽 메뉴에서 'Applications'을 선택하고 (메인 메뉴 항목 'Applications' 아래에 있음) 그 다음에 'Auth0 Management API (Test Application)'라는 이름의 애플리케이션을 선택.
11. `auth0/env.bash` 파일을 열고 위의 화면에서 다음 값들을 복사.
- Domain 값을 TENANT 변수의 값으로 설정.
- Client ID를 MGM_CLIENT_ID 변수의 값으로 설정.
- Client Secret을 MGM_CLIENT_SECRET 변수의 값으로 설정.
- ???: USERNAME, PASSWORD 설정 안함

> 이런 식으로 사용자의 비밀번호를 지정하는 것은 보안 측면에서 좋은 방법은 아니다. Auth0는 사용자가 스스로 비밀번호를 설정할 수 있는 사용자 등록을 지원하지만, 설정하는 데 더 많은 작업이 필요하다. 자세한 정보는 https://auth0.com/docs/connections/database/password-change 를 참조. 이것은 테스트 목적으로만 사용되므로 이렇게 비밀번호를 지정하는 것도 괜찮다.

다음 정의들을 생성할 스크립트를 기동시킬 수 있다.

> - 두 개의 애플리케이션, reader와 writer 또는 OAuth 용어로는 클라이언트.
> - OAuth 용어로는 리소스 서버인 product-composite API, OAuth 범위인 product:read 및 product:write가 있다.
> - 우리가 인증 코드 부여 흐름을 테스트하는데 사용할 OAuth 용어로 리소스 소유자인 사용자.
> - 마지막으로, reader 애플리케이션에 product:read 스코프를 부여하고, writer 애플리케이션에는 product:read 및 product:write 스코프를 부여할 것이다.

1. 다음 커맨드를 실행하라.
```shell
cd auth0
./setup-tenant.bash
```
결과:
```shell
Auth0 - OAuth2 settings:

export TENANT=dev-r8utd4y3vz68zlnd.us.auth0.com
export WRITER_CLIENT_ID=...
export WRITER_CLIENT_SECRET=...
export READER_CLIENT_ID=...
export READER_CLIENT_SECRET=...
```

2. 이후 테스트에 사용하기 위해 export 커맨드를 카피해두자.
3. 테스트 사용자에게 지정된 이메일을 확인하라. 'Verify your email.'라는 제목의 메일을 받게 될 것이다. 이메일에 있는 지침을 사용하여 테스트 사용자의 이메일 주소를 인증하라.

> `Note` 상기 스크립트는 몇 번을 실행하더라도 똑같은 결과가 나온다.

> `setup-tenant.bash`를 통해 생성한 오브젝트를 지우고 싶다면 `reset-tenant.bash`를 사용하라.

#### OAuth 2.0 리소스 서버 설정 변경

이미 설명했듯이, OpenID Connect 제공자를 사용할 때 OAuth 리소스 서버에서 표준화된 디스커버리 엔드포인트에 대한 base URI만 구성하면 된다.
product-composite 프로젝트와 gateway 프로젝트에서 OIDC 디스커버리 엔드포인트를 로컬 인증 서버가 아닌 Auth0를 가리키도록 수정한다. 두 프로젝트의 application.yml 파일에 다음을 변경하자.

1. `spring.security.oauth2.resourceserver.jwt.issuer-uri` 프로퍼티를 찾는다.
2. 그 값을 https://${TENANT}/로 바꾼다. 여기서 ${TENANT}는 자신의 테넌트 도메인 이름으로 대체되어야 한다. 제 경우에는 dev-ml.eu.auth0.com입니다. 마지막의 /를 잊지 말라.

ex) `spring.security.oauth2.resourceserver.jwt.issuer-uri: https://dev-r8utd4y3vz68zlnd.us.auth0.com/`

product-composite와 gateway 프로젝트를 다시 빌드하고 실행한다.
```shell
./gradlew build && docker-compose up -d --build product-composite gateway
```

#### Auth0으로부터 액세스 토큰을 얻기 위한 테스트 스크립트 `test-em-all.bash` 수정

1. 다음 커맨드를 찾는다.
```shell
ACCESS_TOKEN=$(curl -k https://writer:secret-writer@$HOST:$PORT/oauth2/token -d grant_type=client_credentials -d scope="product:read product:write" -s | jq .access_token -r)
```

2. 다음 커맨드로 대체한다.
```shell
export TENANT=...
export WRITER_CLIENT_ID=...
export WRITER_CLIENT_SECRET=...
ACCESS_TOKEN=$(curl -X POST https://$TENANT/oauth/token \
  -d grant_type=client_credentials \
  -d audience=https://localhost:8443/product-composite \
  -d scope=product:read+product:write \
  -d client_id=$WRITER_CLIENT_ID \
  -d client_secret=$WRITER_CLIENT_SECRET -s | jq -r .access_token)
```

> `Tip` 상기 커맨드에서 Auth0는 요청된 액세스 토큰의 예상 수신자(audience)를 지정하도록 요구한다. 이는 추가적인 보안 계층이다. audience는 우리가 액세스 토큰을 사용하여 호출하려는 API다. API 구현이 audience 필드를 검증한다면, 이것은 누군가가 다른 목적으로 발행된 액세스 토큰을 사용하여 API에 접근하려고 시도하는 상황을 방지할 것이다.

3. 앞서 언급한 명령에서 환경 변수 TENANT, WRITER_CLIENT_ID, WRITER_CLIENT_SECRET의 값을 setup-tenant.bash 스크립트에서 반환된 값으로 설정하라.

4. 다음 커맨드를 찾는다.
```shell
READER_ACCESS_TOKEN=$(curl -k https://reader:secret-reader@$HOST:$PORT/oauth2/token -d grant_type=client_credentials -d scope="product:read" -s | jq .access_token -r)
```

5. 다음 커맨드로 대체한다.
```shell
export READER_CLIENT_ID=...
export READER_CLIENT_SECRET=...
READER_ACCESS_TOKEN=$(curl -X POST https://$TENANT/oauth/token \
  -d grant_type=client_credentials \
  -d audience=https://localhost:8443/product-composite \
  -d scope=product:read \
  -d client_id=$READER_CLIENT_ID \
  -d client_secret=$READER_CLIENT_SECRET -s | jq -r .access_token)
```

6. 상기 커맨드에서 환경 변수 READER_CLIENT_ID와 READER_CLIENT_SECRET의 값을 setup-tenant.bash 스크립트에서 반환된 값으로 설정한다.

이제 액세스 토큰은 로컬 인증 서버가 아닌 Auth0에서 발행되며, API 구현체는 application.yml 파일에 구성된 Auth0의 디스커버리 서비스로부터 정보를 사용하여 액세스 토큰을 검증할 수 있다.
API 구현체는 이전과 같이 액세스 토큰의 스코프를 사용하여 클라이언트가 API 호출을 수행할 권한을 부여하거나 부여하지 않을 수 있습니다.
이렇게 하면 필요한 모든 변경 사항이 적용됩니다. Auth0에서 액세스 토큰을 얻을 수 있는지 확인하기 위해 몇 가지 테스트를 실행해 봅시다.


### Auth0를 OpenID Connect 제공자로 사용하여 테스트 스크립트 실행 (Running the test script with Auth0 as the OpenID Connect provider)

```shell
docker-compose build
./test-em-all.bash start
docker-compose logs product-composite | grep "Authorization info"
```

1. `product:read`와 `product:write` 스코프를 통해 얻은 결과
```shell
s.m.m.c.p.s.ProductCompositeServiceImpl  : Authorization info: Subject: ...@clients, scopes: product:read product:write, expires 2023-09-22T02:00:00Z: issuer: https://dev-r8utd4y3vz68zlnd.us.auth0.com/, audience: [https://localhost:8443/product-composite]
```
2. `product:read` 스코프를 통해 얻은 결과
```shell
s.m.m.c.p.s.ProductCompositeServiceImpl  : Authorization info: Subject: ...@clients, scopes: product:read, expires 2023-09-22T02:00:05Z: issuer: https://dev-r8utd4y3vz68zlnd.us.auth0.com/, audience: [https://localhost:8443/product-composite]
```

#### Auth0을 통해 직접 액세스 토큰을 얻고 싶은 경우

`setup-tenant.bash`를 통해 얻은 WRITER_CLIENT_ID, WRITER_CLIENT_SECRET을 환경변수에 설정하고 다음 커맨드를 실행하면 액세스 토큰을 얻을 수 있다.
```shell
export TENANT=...
export WRITER_CLIENT_ID=...
export WRITER_CLIENT_SECRET=...
curl -X POST https://$TENANT/oauth/token \
  -d grant_type=client_credentials \
  -d audience=https://localhost:8443/product-composite \
  -d scope=product:read+product:write \
  -d client_id=$WRITER_CLIENT_ID \
  -d client_secret=$WRITER_CLIENT_SECRET
```

#### 인가 코드 그랜트 플로우를 통해 액세스 토큰을 얻고 싶은 경우
1. 인카 코드를 얻기 위해서 아래의 주소를 웹 브라우저에 입력한다.
   `https://${TENANT}/authorize?audience=https://localhost:8443/product-composite&scope=openid email product:read product:write&response_type=code&client_id=${WRITER_CLIENT_ID}&redirect_uri=https://my.redirect.uri&state=845361`

2. ${TENANT}와 ${WRITER_CLIENT_ID}는 setup-tenant.bash 스크립트에서 반환된 값으로 대체한다.
3. Auth0의 로그인 스크린이 표시된다. 사용자 이름과 비밀번호를 입력하고 로그인한다.
4. 로그인 성공 시, Auto0는 클라이언트 어플리케이션 승인을 묻는다.

![](./images/img_9.png)

5. 승인시 지정했던 리다이렉트 URI로 code 파라미터와 함께 연결된다.
```text
https://my.redirect.uri/?code=qt7DGgvrH6W1...&state=845361
```

6. code 값을 추출하여 다음 커맨드를 실행한다.
```shell
CODE=...
export TENANT=...
export WRITER_CLIENT_ID=...
export WRITER_CLIENT_SECRET=...
curl -X POST https://$TENANT/oauth/token \
 -d grant_type=authorization_code \
 -d client_id=$WRITER_CLIENT_ID \
 -d client_secret=$WRITER_CLIENT_SECRET  \
 -d code=$CODE \
 -d redirect_uri=https://my.redirect.uri -s | jq .
```
결과:
```shell
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsIm...",
  "id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6...",
  "scope": "openid email product:read product:write",
  "expires_in": 86400,
  "token_type": "Bearer"
}
```

### Auth0 액세스 토큰을 통한 보호된 API 호출

Readonly API 호출
```shell
ACCESS_TOKEN=...
curl https://localhost:8443/product-composite/1 -k -H "Authorization: Bearer $ACCESS_TOKEN" -i
```

Update API 호출
```shell
ACCESS_TOKEN=...
curl https://localhost:8443/product-composite/999 -k -H "Authorization: Bearer $ACCESS_TOKEN" -X DELETE -i 
```

### 사용자 정보 엔드포인트를 사용하여 사용자에 대한 더 많은 정보 얻기

```shell
Export TENANT=...
curl -H "Authorization: Bearer $ACCESS_TOKEN" https://$TENANT/userinfo -s | jq
```
결과:
```shell
{
  "sub": "auth0|...",
  "email": "tintachina84@gmail.com",
  "email_verified": true
}
```

> `Tip` 상기 엔드포인트는 사용자가 Auth0의 액세스 토큰이 취소되지 않았는지 검증하는데 쓰일 수도 있다.

#### 테스트 종료
```shell
docker-compose down
```


## 설정 중앙화 (Centralized Configuration)

이 장에서는 설정 중앙화를 위해 Spring Cloud Config Server를 사용하는 방법을 설명한다.
> - Spring Cloud Config Server 소개
> - Config Server 설정하기
> - Config Server의 클라이언트 구성하기
> - 설정 저장소 구조화하기
> - Spring Cloud Config Server 사용해보기

Spring Cloud Config Server는 여타 마이크로서비스들과 마찬가지로 Edge Server 뒤에 존재한다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_12_01.png)

Config server를 만들 때 다음과 같은 고려사항이 있다.
> - 설정 저장소에 대한 저장소 유형 선택하기
> - 초기 클라이언트 연결 방식 결정하기, 설정 서버 또는 디스커버리 서버로의 연결
> - API에 대한 무단 액세스로부터 설정 보호하기 및 설정 저장소에 민감한 정보를 평문으로 저장하지 않도록 보안 설정

### 설정 저장소에 대한 저장소 유형 선택하기

Config Server는 설정 정보를 저장하기 위해 여러 유형의 저장소를 지원한다. 다음은 그 예이다.
> - Git repository
> - Local filesystem
> - HashiCorp Vault
> - JDBC database

여기서는 로컬 파일 시스템을 사용할 것이다. 로컬 파일 시스템을 사용하기 위해서는 설정 서버를 Native Spring Profile로 실행해야 한다.  
설정 저장소의 위치는 spring.cloud.config.server.native.searchLocations 프로퍼티에 지정된다.

### 초기 클라이언트 연결 방식 결정하기
기본적으로 클라이언트는 설정 정보를 가져오기 위해 먼저 설정 서버에 연결한다. 그런 다음 구성에 따라 Netflix Eureka와 같은 디스커버리 서버에 등록한다. 반대로 진행할 수도 있다.
즉, 클라이언트가 먼저 디스커버리 서버에 연결하여 설정 서버 인스턴스를 찾고, 그런 다음 설정을 가져오기 위해 설정 서버에 연결하는 방식이다. 양쪽 접근 방식 모두 장단점이 있다.
여기서는 클라이언트가 먼저 설정 서버에 연결할 것이다. 이 접근 방식을 사용하면 디스커버리 서버의 설정을 설정 서버에 저장할 수 있게 된다.

### 설정 보안 (Securing the configuration)

일반적으로 설정 정보는 민감한 정보로 간주된다. 이는 설정 정보를 전송 및 저장 시에 보호해야 함을 의미한다.
런타임 관점에서 설정 서버는 엣지 서버를 통해 외부에 노출될 필요가 없다. 그러나 개발 중에는 설정을 확인하기 위해 설정 서버의 API에 액세스할 수 있는 것이 유용하다.
프로덕션 환경에서는 외부 액세스를 제한하는 것이 권장된다.

#### 설정 정보의 전송 중 보안 강화

설정 정보를 마이크로서비스나 설정 서버의 API를 사용하는 사용자가 요청할 때, 이미 엣지 서버가 HTTPS를 사용하고 있기 때문에 도청으로부터 보호된다.
API 사용자가 알려진 클라이언트인지 확인하기 위해 HTTP basic authentication을 사용할 것이다. 설정 서버에서 Spring Security를 사용하여 HTTP basic authentication을 설정하고,
허용된 자격 증명으로 SPRING_SECURITY_USER_NAME 및 SPRING_SECURITY_USER_PASSWORD 환경 변수를 지정함으로써 HTTP basic authentication을 설정할 수 있다.

#### 설정 정보 저장시의 보안 강화
설정 저장소에 액세스 권한이 있는 사람이 비밀번호와 같은 민감한 정보를 도난할 수 있는 상황을 피하기 위해, 설정 서버는 디스크에 저장될 때 설정 정보의 암호화를 지원한다.
설정 서버는 대칭 및 비대칭 키 모두를 사용할 수 있습니다. 비대칭 키는 더 안전하지만 관리가 어렵다.
여기서는 대칭 키를 사용한다. 대칭 키는 환경 변수인 ENCRYPT_KEY를 지정하여 설정 서버에 시작 시 제공된다. 암호화된 키는 민감한 정보와 마찬가지로 보호되어야 하는 일반 문자열이다.

### 설정 서버 API 소개


---
### 설정 서버 구성하기
1. 스프링부트 스켈레톤 프로젝트 생성
2. `spring-cloud-config-server`  `spring-boot-starter-security` 의존성 추가
3. `@EnableConfigServer` 어노테이션 추가
```java
@EnableConfigServer
@SpringBootApplication
public class ConfigServerApplication {
```

4. application.yml 에 설정 서버를 위한 설정 추가
```yaml
server.port: 8888
spring.cloud.config.server.native.searchLocations: file:${PWD}/config-repo
management.endpoint.health.show-details: "ALWAYS"
management.endpoints.web.exposure.include: "*"
logging:
  level:
    root: info
---
spring.config.activate.on-profile: docker
spring.cloud.config.server.native.searchLocations: file:/config-repo
```

5. 마이크로서비스 랜드스케이프 외부에서 설정 서버의 API에 접근할 수 있도록 엣지 서버에 라우팅 규칙을 추가한다.
6. 각각의 Docker Compose 파일에 Dockerfile과 설정 서버 정의를 추가한다.
7. 민감한 설정 매개변수를 표준 Docker Compose 환경 파일인 .env로 외부화한다.
8. 공통 빌드 파일 `settings.gradle`에 설정 서버 프로젝트를 추가한다.
```java
include ':spring-cloud:config-server'
```

### 엣지 서버에 라우팅 룰 추가
```yaml
 - id: config-server
   uri: http://${app.config-server}:8888
  predicates:
  - Path=/config/**
  filters:
  - RewritePath=/config/(?<segment>.*), /$\{segment}
```

라우팅 규칙의 RewritePath 필터는 들어오는 URL에서 선행 부분인 /config를 제거한 후 설정 서버로 전송한다.
엣지 서버는 또한 모든 요청을 설정 서버로 허용하도록 구성되어 있으며, 보안 검사를 설정 서버에 위임합니다. 다음 줄은 엣지 서버의 SecurityConfig 클래스에 추가된다.
```java
  .pathMatchers("/config/**").permitAll()
```

이 라우팅 규칙이 적용되면 구성 서버의 API를 사용할 수 있다. 예를 들어, 다음 명령을 실행하여 도커 Spring 프로필을 사용하는 product 서비스의 설정을 요청할 수 있다.
```shell
curl https://dev-usr:dev-pwd@localhost:8443/config/product/docker -ks | jq
```

### 도커 사용을 위한 설정 서버 구성하기

설정 서버의 Dockerfile은 다른 마이크로서비스와 동일하다. 단, 포트 8080 대신 포트 8888을 노출하는 점이 다르다.
설정 서버를 Docker Compose 파일에 추가하는 방법은 다른 마이크로서비스와 약간 다르다.
```yaml
config-server:
  build: spring-cloud/config-server
  mem_limit: 512m
  environment:
    - SPRING_PROFILES_ACTIVE=docker,native
    - ENCRYPT_KEY=${CONFIG_SERVER_ENCRYPT_KEY}
    - SPRING_SECURITY_USER_NAME=${CONFIG_SERVER_USR}
    - SPRING_SECURITY_USER_PASSWORD=${CONFIG_SERVER_PWD}
  volumes:
    - $PWD/config-repo:/config-repo
```

1. Spring 프로필인 native는 설정 서버에게 설정 저장소가 로컬 파일을 기반으로 한다는 신호로 추가된다.
2. 환경 변수 ENCRYPT_KEY는 설정 서버가 민감한 구성 정보를 암호화하고 복호화하는 데 사용할 대칭 암호화 키를 지정하는 데 사용된다.
3. 환경 변수 SPRING_SECURITY_USER_NAME과 SPRING_SECURITY_USER_PASSWORD는 HTTP  basic authentication을 사용하여 API를 보호하는 데 사용할 자격 증명을 지정하는 데 사용된다.
4. volumes 선언은 /config-repo 경로에서 Docker 컨테이너 내에서 config-repo 폴더에 접근할 수 있도록 한다.

앞서 언급한 세 개의 환경 변수의 값은 Docker Compose 파일에서 ${...}으로 표시되며, Docker Compose는 이 값을 .env 파일에서 가져온다.
```shell
CONFIG_SERVER_ENCRYPT_KEY=my-very-secure-encrypt-key
CONFIG_SERVER_USR=dev-usr
CONFIG_SERVER_PWD=dev-pwd
```

> `Tip` .env 파일에 저장된 정보, 즉 사용자 이름, 비밀번호 및 암호화 키는 민감한 정보이므로 개발 및 테스트 외의 용도로 사용될 경우 보호되어야 한다.
> 또한, 암호화 키를 분실하면 구성 저장소의 암호화된 정보를 복호화할 수 없는 상황이 발생할 수 있음에 유의해야 한다.


### 설정 서버 클라이언트 구성하기
마이크로서비스가 설정 서버에서 설정을 가져올 수 있도록 하려면 마이크로서비스를 업데이트해야 한다. 이를 위해 다음을 수행해야 한다.

1. Gradle 빌드 파일인 build.gradle에 spring-cloud-starter-config 및 spring-retry 종속성을 추가한다.
2. 술정 파일인 application.yml을 설정 저장소로 이동하고, 프로퍼티 spring.application.name에 지정된 클라이언트 이름으로 이름을 변경한다.
3. src/main/resources 폴더에 새로운 application.yml 파일을 추가한다. 이 파일은 설정 서버에 연결하기 위해 필요한 설정을 보유할 때 사용된다.
4. 예를 들어, product 서비스의 경우 Docker Compose 파일에 설정 서버에 액세스하는 데 사용되는 자격 증명을 추가한다.

```yaml
product:
  environment:
    - CONFIG_SERVER_USR=${CONFIG_SERVER_USR}
    - CONFIG_SERVER_PWD=${CONFIG_SERVER_PWD}
```

5. Spring Boot 기반 자동화된 테스트 실행 시 설정 서버 사용을 비활성화하려면 @DataMongoTest, @DataJpaTest, @SpringBootTest 어노테이션에 spring.cloud.config.enabled=false를 추가한다.

```java
@DataMongoTest(properties = {"spring.cloud.config.enabled=false"})
@DataJpaTest(properties = {"spring.cloud.config.enabled=false"})
@SpringBootTest(webEnvironment=RANDOM_PORT, properties = {"eureka.client.enabled=false", "spring.cloud.config.enabled=false"})
```

### 연결 정보 구성하기
src/main/resources/application.yml 파일은 이제 설정 서버에 연결하기 위해 필요한 클라이언트 설정을 가지고 있다.
이 파일은 spring.application.name 속성에 지정된 애플리케이션 이름을 제외하고는 모든 클라이언트에 대해 동일한 내용을 갖는다.
아래 예시에서는 애플리케이션 이름을 'product'로 설정했다.

```yaml
spring.config.import: "configserver:"
spring:
  application.name: product
  cloud.config:
    failFast: true
    retry:
      initialInterval: 3000
      multiplier: 1.3
      maxInterval: 10000
      maxAttempts: 20
    uri: http://localhost:8888
    username: ${CONFIG_SERVER_USR}
    password: ${CONFIG_SERVER_PWD}
---
spring.config.activate.on-profile: docker
spring.cloud.config.uri: http://config-server:8888
```

1. Docker 외부에서 실행될 때는 http://localhost:8888 URL을 사용하여 구성 서버에 연결하고, Docker 컨테이너 내에서 실행될 때는 http://config-server:8888 URL을 사용하여 구성 서버에 연결한다.
2. 클라이언트의 사용자 이름과 비밀번호로 CONFIG_SERVER_USR 및 CONFIG_SERVER_PWD 속성의 값에 기반한 HTTP basic authentication을 사용한다.
3. 필요한 경우 시작 시 20번까지 설정 서버에 재연결을 시도.
4. 연결 시도가 실패한 경우 클라이언트는 처음으로 3초 동안 대기한 후 재연결을 시도. 
5. 다음 재시도를 위한 대기 시간은 1.3배씩 증가.
6. 연결 시도 간 최대 대기 시간은 10초.
7. 클라이언트가 20번의 시도 후에도 설정 서버에 연결할 수 없으면 시작이 실패한다.

이 구성은 일반적으로 설정 서버와의 일시적인 연결 문제에 대한 회복력을 갖추기에 좋다. 이는 특히 마이크로서비스의 전체 랜드스케이프와 그 설정 서버가 한 번에 시작될 때, 예를 들어 'docker-compose up' 명령을 사용할 때 유용하다.
이 시나리오에서 많은 클라이언트들이 준비가 되기 전에 설정 서버에 연결하려고 시도하고, 재시도 로직은 설정 서버가 실행되고 나면 클라이언트들이 성공적으로 연결하게 한다.

### 설정 저장소 구조화하기
각 클라이언트의 소스 코드에서 설정 파일을 설정 저장소로 이동한 후에는, 예를 들어, actuator 엔드포인트의 구성 및 Eureka, RabbitMQ, Kafka에 연결하는 방법에 대한 설정 등 많은 설정 파일에서 공통된 설정을 가질 것이다.
공통 부분은 application.yml이라는 공통 설정 파일에 위치하게 된다. 이 파일은 모든 클라이언트가 공유한다. 설정 저장소는 다음과 같은 파일들을 포함하고 있다.

```text
config-repo/
├── application.yml
├── auth-server.yml
├── eureka-server.yml
├── gateway.yml
├── product-composite.yml
├── product.yml
├── recommendation.yml
└── review.yml
```

### Spring Cloud Config Server 사용해보기

> - 먼저, 소스에서 빌드하고 테스트 스크립트를 실행하여 모든 것이 잘 맞아떨어지는지 확인한다.
> - 다음으로, 마이크로서비스의 설정을 검색하기 위해 설정 서버 API를 시험해 본다.
> - 마지막으로, 예를 들어 비밀번호와 같은 민감한 정보를 암호화하고 복호화하는 방법을 살펴본다.

### 자동화 테스트
1. 빌드 도커 이미지
```shell
./gradlew build && docker-compose build
```

2. Docker에서 시스템 랜드스케이프 기동 & 테스트
```shell
./test-em-all.bash start
```


## Improving Resilience Using Resilience4j

서킷 브레이커, 타임 리미터, 그리고 재시도 메커니즘은 두 소프트웨어 컴포넌트 간의 동기식 통신에서 유용하게 사용될 수 있다.
예를 들면, 마이크로서비스와 같은 경우다. 이번에는 이러한 메커니즘들을 한 곳에 적용해보겠다. 바로 'product-composite' 서비스가 'product' 서비스에 요청을 보내는 곳이다.

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_13_01.png)

### 서킷 브레이커 (Circuit Breaker)

![](https://static.packt-cdn.com/products/9781805128694/graphics/Images/B19825_13_02.png)

> - 서킷 브레이커가 너무 많은 오류를 감지하면 회로를 열게 된다. 즉, 새로운 호출을 허용하지 않는다.
> - 회로가 열린 상태에서 서킷 브레이커는 fail-fast 로직을 수행한다. 이는 후속 호출에서 새로운 오류, 예를 들어 타임아웃 등이 발생하는 것을 기다리지 않고, 대신에 호출을 바로 폴백(fallback: 어떤 기능이 약해지거나 제대로 동작하지 않을 때, 이에 대처하는 기능 또는 동작) 메소드로 리디렉션한다는 의미이다.
    폴백 메소드는 다양한 비즈니스 로직을 적용하여 최선의 노력으로 응답을 생성할 수 있다. 예를 들어, 폴백 메소드는 로컬 캐시에서 데이터를 반환하거나 단순히 즉시 오류 메시지를 반환할 수 있다.
    이것은 해당 서비스가 의존하는 다른 서비스들이 정상적으로 응답하지 않으면 마이크로서비스가 반응하지 않게 되는 것을 방지한다. 특히 고부하 상황에서 유용하다.
> - 일정 시간 후에 서킷 브레이커는 'half-open' 상태가 되어 실패 원인이 사라진 경우인지 확인하기 위해 새로운 호출들을 허용한다. 만약 서킷 브레이커가 새롭게 발생한 실패들을 감지한다면, 다시 회로를 열고 fail-fast 로직으로 돌아간다.
    그렇지 않다면, 회로를 닫고 정상 작동 상태로 돌아간다. 이러한 작동 방식은 마이크로서비스가 장애에 대해 복원력있다거나 자체 복구능력(self-healing)를 가진다고 할 수 있으며, 이런 기능은 서로 동기식으로 통신하는 마이크로서비스의 시스템 환경에서 필수적이다.


Resilience4j는 런타임 시점에서 여러 방법으로 서킷브레이커의 정보를 노출시키고 있다.

> - 서킷 브레이커의 현재 상태는 마이크로서비스의 액추에이터(actuator) 건강 상태 엔드포인트인 '/actuator/health'를 사용하여 모니터링할 수 있다.
> - 또한 서킷 브레이커는 액추에이터 엔드포인트에서 이벤트를 발행합니다. 예를 들어, 상태 전환과 '/actuator/circuitbreakerevents' 등이다.
> - 마지막으로, 서킷 브레이커는 Spring Boot의 메트릭 시스템과 통합되어 있으며, 이를 사용하여 Prometheus와 같은 모니터링 도구에 메트릭을 발행할 수 있다.

Resilience4j의 설정값에 대한 설명:

> - slidingWindowType: 서킷 브레이커가 열릴 필요가 있는지 판단하기 위해, Resilience4j는 슬라이딩 윈도우를 사용하여 최근의 이벤트를 계산한다. 슬라이딩 윈도우는 고정된 수의 호출 또는 고정된 경과 시간을 기반으로 할 수 있다. 이 매개변수는 어떤 유형의 슬라이딩 윈도우를 사용할지 설정하는 데 사용된다. COUNT_BASED로 설정하여 횟수 기반의 슬라이딩 윈도우를 사용한다.
> - slidingWindowSize: 회로가 열려야 하는지 여부를 결정하는 데 사용되는 닫힌 상태에서의 호출 수. 이 매개변수를 5로 설정한다.
> - failureRateThreshold: 서킷을 열게 될 실패한 호출에 대한 백분율 임계값. 이 매개변수를 50%로 설정한다. 이 설정은 slidingWindowSize가 5로 설정된 것과 함께, 마지막 다섯 번의 호출 중 세 번 이상이 오류인 경우에 서킷을 열게 된다.
> - automaticTransitionFromOpenToHalfOpenEnabled: 대기 기간이 종료되면 서킷 브레이커가 자동으로 half-open 상태로 전환할 지 여부를 결정한다. 그렇지 않으면, 대기 기간 종료 후 첫 번째 호출까지 half-open 상태로 전환하기 위해 기다린다. 이 매개변수를 true로 설정한다.
> - waitDurationInOpenState: 회로가 open 상태인 즉, half-open 상태로 전환하기 전에 얼마나 오래 지속되어야 하는지 명시한다. 이 매개변수를 10000ms(10초) 로 설정한다.
> - permittedNumberOfCallsInHalfOpenState: half-open 상태에서의 호출 횟수로, 이는 회로가 다시 열릴지 아니면 정상적인 닫힌 상태로 돌아갈지 결정하는 데 사용된다. 이 매개변수를 3으로 설정한다. 즉, 서킷 브레이커는 회로가 half-open 상태로 전환된 후 첫 세 번의 호출을 기반으로 회로가 열릴지 닫힐지를 결정하게 된다. failureRateThreshold 매개변수가 50%로 설정되어 있으므로, 세 번의 호출 중 두 번이나 모두 실패하면 회로는 다시 열린다. 그렇지 않으면, 회로는 닫힌다.
> - ignoreExceptions: 오류로 간주되어서는 안 되는 예외 사항을 명시하는데 사용할 수 있다.
> - registerHealthIndicator = true : Resilience4j에서 헬스 체크 엔드포인트에 서킷 브레이커의 상태에 대한 정보를 채우도록 활성화한다.
> - allowHealthIndicatorToFail = false : Resilience4j에게 헬스 체크 엔드포인트의 상태에 영향을 주지 않도록 지시한다. 헬스 체크 엔드포인트가 여전히 'UP'으로 보고되며, 컴포넌트의 서킷 브레이커 중 하나가 open 또는 half-open 상태라 해도 마찬가지이다.
> - 마지막으로 Resilience4j가 제공하는 서킷브레이커 헬스 체크 엔드포인트를 스프링부트 액츄에이터에 추가한다.
```yaml
management.health.circuitbreakers.enabled: true
```

### 타임 리미터 (Time Limiter)
서킷 브레이커가 느리거나 반응하지 않는 서비스를 처리하는 데 도움이 되도록, 타임아웃 메커니즘이 유용할 수 있다.
Resilience4j의 타임아웃 메커니즘인 TimeLimiter는 표준 Spring Boot 설정 파일을 사용하여 구성할 수 있다.

> - timeoutDuration: TimeLimiter 인스턴스가 호출 완료를 기다리는 시간을 명시. 이 시간이 지나면 타임아웃 예외가 발생한다. 이 값을 2초로 설정한다.

### 재시도 (Retry) 메커니즘
재시도 메커니즘은 일시적인 네트워크 문제와 같은 랜덤하고 드물게 발생하는 오류에 매우 유용하다. 재시도 메커니즘은 실패한 요청을 시도 사이에 설정 가능한 지연 시간을 두고 여러 번 재시도할 수 있다.
재시도 메커니즘의 사용에는 매우 중요한 제한 조건이 있는데, 그것은 재시도하는 서비스가 멱등성(idempotent)을 가져야 한다는 것이다. 즉, 동일한 요청 매개변수로 서비스를 한 번이나 여러 번 호출하면 결과가 동일해야 한다.
예를 들어, 정보를 읽는 것은 멱등성을 가지지만, 정보를 생성하는 것은 대체로 그렇지 않다. 첫 번째 주문 생성의 응답이 네트워크에서 손실했기 때문에 재시도 메커니즘이 실수로 두 개의 주문을 만들어내는 상황은 바람직하지 않다.

Resilience4j는 이벤트와 메트릭에 대해 서킷 브레이커와 같은 방식으로 재시도 정보를 제공하지만 건강 정보(health information)는 제공하지 않는다.
재시도 이벤트는 액추에이터 엔드포인트 '/actuator/retryevents'에서 접근할 수 있다. Resilience4j의 표준 Spring Boot 설정 파일을 사용하여 구성함으로써 재시도 로직을 제어할 수 있다.

> - maxAttempts: 첫 번째 호출을 포함하여 포기하기 전까지의 시도 횟수. 이 매개변수를 3으로 설정하여, 초기 실패 호출 후 최대 두 번의 재시도를 허용한다.
> - waitDuration: 다음 재시도 시도 전 대기 시간. 이 값을 1000ms로 설정한다. 즉, 재시도 사이에 1초 동안 대기하게 된다.
> - retryExceptions: 재시도를 트리거할 예외 목록. 우리는 HTTP 요청이 500 상태 코드로 응답할 때, 즉 InternalServerError 예외 발생 시에만 재시도를 트리거하게 된다.

### Resilience(탄력성) 메커니즘 소스코드에 추가하기
> - 빌드 파일에 Resilience4j에 대한 스타터 의존성을 추가
> - 탄력성 메커니즘이 적용될 소스 코드에 주석을 추가
> - 탄력성 메커니즘의 동작을 제어하는 설정을 추가

의존성 추가
```groovy
ext {
   resilience4jVersion = "2.0.2"
}
dependencies {
    implementation "io.github.resilience4j:resilience4j-spring-
boot2:${resilience4jVersion}"
    implementation "io.github.resilience4j:resilience4j-reactor:${resilience4jVersion}"
    implementation 'org.springframework.boot:spring-boot-starter-aop'
    ...
```

예전 버전의 Resilience4j 를 덮어쓰지 않기 위해 BOM 추가
```groovy
dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
        mavenBom "io.github.resilience4j:resilience4j-bom:${resilience4jVersion}"
    }
}
```

`@CircuitBreaker` 어노테이션을 붙임으로써 서킷브레이커를 적용할 수 있다. ProductCompositeIntegration 클래스의 getProduct() 메소드에 적용해보자.
서킷브레이커는 타임아웃이 아닌 예외에 의해 발생한다. 타임아웃에 의해서도 서킷브레이커를 발동시키기 위해 @TimeLimiter(...) 어노테이션을 추가한다.
```java
@TimeLimiter(name = "product")
@CircuitBreaker(
     name = "product", fallbackMethod = "getProductFallbackValue")
public Mono<Product> getProduct(
  int productId, int delay, int faultPercent) {
  ...
}
```

#### fail-fast 폴백 로직
```java
private Mono<Product> getProductFallbackValue(int productId,
        int delay, int faultPercent, CallNotPermittedException ex) {
        if (productId == 13) {
        String errMsg = "Product Id: " + productId
        + " not found in fallback cache!";
        throw new NotFoundException(errMsg);
        }
        return Mono.just(new Product(productId, "Fallback product"
        + productId, productId, serviceUtil.getServiceAddress()));
        }
```

### 설정 추가 (product-composite.yml)
```yaml
resilience4j.timelimiter:
  instances:
    product:
      timeoutDuration: 2s
management.health.circuitbreakers.enabled: true
resilience4j.circuitbreaker:
  instances:
    product:
      allowHealthIndicatorToFail: false
      registerHealthIndicator: true
      slidingWindowType: COUNT_BASED
      slidingWindowSize: 5
      failureRateThreshold: 50
      waitDurationInOpenState: 10000
      permittedNumberOfCallsInHalfOpenState: 3
      automaticTransitionFromOpenToHalfOpenEnabled: true
      ignoreExceptions:
        - se.magnus.api.exceptions.InvalidInputException
        - se.magnus.api.exceptions.NotFoundException
```

### Retry 메커니즘 추가
```java
  @Retry(name = "product")
  @TimeLimiter(name = "product")
  @CircuitBreaker(name = "product", fallbackMethod =
    "getProductFallbackValue")
  public Mono<Product> getProduct(int productId, int delay, 
    int faultPercent) {
```
```yaml
resilience4j.retry:
  instances:
    product:
      maxAttempts: 3
      waitDuration: 1000
      retryExceptions:
      - org.springframework.web.reactive.function.client.WebClientResponseException$InternalServerError
```

### 자동화 테스트

필요한 검증을 수행하기 위해서는, 에지 서버를 통해 노출되지 않은 product-composite 마이크로서비스의 액추에이터 엔드포인트에 접근할 필요가 있다.
따라서, Docker Compose exec 명령어를 사용하여 product-composite 마이크로서비스에서 명령을 실행하여 액추에이터 엔드포인트에 접근한다.
마이크로서비스에서 사용하는 기본 이미지인 Eclipse Temurin은 curl을 번들링하고 있으므로, product-composite 컨테이너에서 curl 명령어를 단순히 실행하여 필요한 정보를 얻을 수 있다.

```shell
docker-compose exec -T product-composite curl -s http://product-composite:8080/actuator/health
```

테스트에 필요한 정보를 추출하기 위해, 출력을 jq 도구로 파이핑할 수 있다. 예를 들어, 서킷 브레이커의 실제 상태를 추출하기 위해 다음 명령어를 실행할 수 있다.
```shell
docker-compose exec -T product-composite curl -s http://product-composite:8080/actuator/health | jq -r .components.circuitBreakers.details.product.details.state
```

실제 상태에 따라 CLOSED, OPEN 또는 HALF_OPEN 중 하나를 반환한다. 테스트는 이렇게 시작되는데, 테스트가 실행되기 전에 서킷 브레이커가 닫혀 있는지 확인한다.
```shell
assertEqual "CLOSED" "$(docker-compose exec -T product-composite curl -s http://product-composite:8080/actuator/health | jq -r .components.circuitBreakers.details.product.details.state)"
```

다음으로, 테스트는 연속으로 세 개의 명령을 실행하여 제품 서비스로부터 느린 응답으로 인한 타임아웃 실패를 발생시켜서 서킷 브레이커를 열게 한다. (지연 매개변수는 3초로 설정됨)
```shell
for ((n=0; n<3; n++))
do
    assertCurl 500 "curl -k https://$HOST:$PORT/product-composite/$PROD_ID_REVS_RECS?delay=3 $AUTH -s"
    message=$(echo $RESPONSE | jq -r .message)
    assertEqual "Did not observe any item or terminal signal within 2000ms" "${message:0:57}"
done
```
> `Tip` 구성에 대한 간단한 알림입니다: 제품 서비스의 타임아웃은 2초로 설정되어 있으므로 3초의 지연은 타임아웃을 발생시킨다. 서킷 브레이커는 닫혔을 때 마지막 다섯 개의 호출을 평가하도록 구성되어 있다.
> 서킷 브레이커와 관련된 테스트 이전에 스크립트에서 이미 몇 번의 성공적인 호출을 수행했다. 실패 임계값은 50%로 설정되어 있으며, 3초 지연이 있는 세 번의 호출은 서킷을 열기에 충분하다.

서킷이 열려 있을 때, 우리는 fail-fast 행동을 기대한다. 즉, 응답을 받기 전에 타임아웃을 기다릴 필요가 없다.
또한 최선의 노력으로 응답을 반환하기 위해 대체 메서드가 호출되기를 기대한다. 이는 정상적인 호출, 즉 지연 요청 없이도 적용되어야 한다.
```shell
assertEqual "OPEN" "$(docker-compose exec -T product-composite curl -s http://product-composite:8080/actuator/health | jq -r .components.circuitBreakers.details.product.details.state)"
assertCurl 200 "curl -k https://$HOST:$PORT/product-composite/$PROD_ID_REVS_RECS?delay=3 $AUTH -s"
assertEqual "Fallback product$PROD_ID_REVS_RECS" "$(echo "$RESPONSE" | jq -r .name)"
assertCurl 200 "curl -k https://$HOST:$PORT/product-composite/$PROD_ID_REVS_RECS $AUTH -s"
assertEqual "Fallback product$PROD_ID_REVS_RECS" "$(echo "$RESPONSE" | jq -r .name)"
```

### 빌드 & 테스트
```shell
./gradlew build && docker-compose build
./test-em-all.bash start
```


### 정상적인 운영 상황에서 서킷이 닫혀있는지 확인
```shell
unset ACCESS_TOKEN
ACCESS_TOKEN=$(curl -k https://writer:secret-writer@localhost:8443/oauth2/token -d grant_type=client_credentials -d scope="product:read product:write" -s | jq -r .access_token)
echo $ACCESS_TOKEN
```

```shell
curl -H "Authorization: Bearer $ACCESS_TOKEN" -k https://localhost:8443/product-composite/1 -w "%{http_code}\n" -o /dev/null -s
```
> `Tip` `-w "%{http_code}\n"` 스위치는 HTTP 응답 상태를 출력하기 위해 사용된다. 200 응답을 반환하는 한, 응답 본문에는 관심이 없으므로 `-o /dev/null` 스위치를 사용하여 응답 본문을 무시한다.

서킷 브레이커가 닫혀있는지 확인하는 health API
```shell
docker-compose exec product-composite curl -s http://product-composite:8080/actuator/health | jq -r .components.circuitBreakers.details.product.details.state
```

### 무언가 잘못되었을 때 서킷 브레이커가 열리도록 강제
이하 API를 세 번 호출하면 3초 지연되는 응답을 매번 발생시키므로 서킷 브레이커가 발동된다.
```shell
curl -H "Authorization: Bearer $ACCESS_TOKEN" -k https://localhost:8443/product-composite/1?delay=3 -s | jq .
```

그럼 네 번째 요청에서 fail-fast 행동, 즉 fallback 메서드가 호출되는지 확인한다. 'Fallback product1' 응답이 오는지 확인한다.

```shell
$ curl -H "Authorization: Bearer $ACCESS_TOKEN" -k https://localhost:8443/product-composite/1?delay=3 -s | jq .
{
  "productId": 1,
  "name": "Fallback product1",
  "weight": 1,
  "recommendations": [
    {
      "recommendationId": 1,
      "author": "author 1",
      "rate": 1,
      "content": "content 1"
    },
    {
      "recommendationId": 2,
      "author": "author 2",
      "rate": 2,
      "content": "content 2"
    },
...
```

> `Tip` fail-fast와 대체 메서드(fallback method)는 서킷 브레이커의 주요 기능이다. 오픈 상태에서 대기 시간을 단 10초로 설정한 구성은 fail-fast 로직과 대체 메서드가 작동하는 것을 보려면 꽤 빠르게 반응해야 한다!
> 반개방(half-open) 상태에 들어간 후에는 항상 타임아웃을 유발하는 세 개의 새로운 요청을 제출할 수 있으며, 이로 인해 서킷 브레이커가 다시 열린 상태로 돌아가고 그 후 네 번째 요청을 빠르게 시도할 수 있다.
> 그럼 그 때 대체 메서드에서 fail-fast 응답을 받게 된다. 대기 시간을 1분 또는 2분으로 증가시킬 수도 있지만, 서킷이 반개방 상태로 전환되기 전에 그만큼의 시간 동안 기다리는 것은 다소 지루할 수 있다.

서킷브레이커가 half-open 상태로 전환되기를 10초 기다린 후, 이하 커맨드로 확인.
```shell
docker-compose exec product-composite curl -s http://product-composite:8080/actuator/health | jq -r .components.circuitBreakers.details.product.details.state
```

### 다시 서킷브레이커 닫기
정상 API를 세번 호출한다.
```shell
curl -H "Authorization: Bearer $ACCESS_TOKEN" -k https://localhost:8443/product-composite/1 -w "%{http_code}\n" -o /dev/null -s
```

모두 200 응답을 받은 후 health API를 호출해본다. `CLOSED` 응답이 나올 것.
```shell
docker-compose exec product-composite curl -s http://product-composite:8080/actuator/health | jq -r .components.circuitBreakers.details.product.details.state
```

마지막 세개의 스테이트 변화 목록을 출력해본다.
```shell
docker-compose exec product-composite curl -s http://product-composite:8080/actuator/circuitbreakerevents/product/STATE_TRANSITION | jq -r '.circuitBreakerEvents[-3].stateTransition, .circuitBreakerEvents[-2].stateTransition, .circuitBreakerEvents[-1].stateTransition'
```

### 무작위 에러를 통한 재시도 메커니즘 테스트

faultPercent 매개변수를 사용하여 이를 수행할 수 있다. 25로 설정하면, 평균적으로 네 번째 요청마다 실패하는 것을 기대한다. 실패한 요청을 자동으로 재시도하는 재시도 메커니즘이 도움을 줄 것을 기대한다.
재시도 메커니즘이 작동했는지 확인하는 한 가지 방법은 curl 명령의 응답 시간을 측정하는 것이다. 정상적인 응답은 약 100ms가 소요된다.
재시도 메커니즘을 1초 대기하도록 구성했으므로(재시도 메커니즘 구성 섹션에서 waitDuration 매개변수 참조), 각 재시도 시도마다 응답 시간이 1초씩 증가할 것으로 예상된다.

```shell
time curl -H "Authorization: Bearer $ACCESS_TOKEN" -k https://localhost:8443/product-composite/1?faultPercent=25 -w "%{http_code}\n" -o /dev/null -s
```
응답:
```shell
time curl -H "Authorization: Bearer $ACCESS_TOKEN" -k https://localhost:8443/product-composite/1?faultPercent=25 -w "%{http_code}\n" -o /dev/null -s
200

real    0m0.091s
```
```shell
time curl -H "Authorization: Bearer $ACCESS_TOKEN" -k https://localhost:8443/product-composite/1?faultPercent=25 -w "%{http_code}\n" -o /dev/null -s
200

real    0m1.128s
```

리트라이 되었을 때 마지막 두개 이벤트 타입 출력해보기
```shell
docker-compose exec product-composite curl -s http://product-composite:8080/actuator/retryevents | jq '.retryEvents[-2], .retryEvents[-1]'
```
응답:
```shell
{
  "retryName": "product",
  "type": "RETRY",
  "creationTime": "2023-09-25T05:17:45.489884255Z[Etc/UTC]",
  "errorMessage": "org.springframework.web.reactive.function.client.WebClientResponseException$InternalServerError: 500 Internal Server Error from GET http://ee5730e685e2:8080/product/1?delay=0&faultPercent=25",
  "numberOfAttempts": 1
}
{
  "retryName": "product",
  "type": "SUCCESS",
  "creationTime": "2023-09-25T05:17:46.499171207Z[Etc/UTC]",
  "errorMessage": "org.springframework.web.reactive.function.client.WebClientResponseException$InternalServerError: 500 Internal Server Error from GET http://ee5730e685e2:8080/product/1?delay=0&faultPercent=25",
  "numberOfAttempts": 1
}
```

### 테스트 종료
```shell
docker-compose down
```





