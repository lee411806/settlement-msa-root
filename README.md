# MSA 전환 및 성능 병목 개선 프로젝트

📅 <b>2025.06.26 ~ 07.18 (3주간) | (1명)</b>



[![Spring Cloud Gateway][Gateway]][Gateway-url]
[![Spring Cloud Config][Config]][Config-url]
[![GitHub Config Repo][GitHubConfigRepo]][GitHubConfigRepo-url]
[![Eureka Server][Eureka]][Eureka-url]
[![Prometheus][Prometheus]][Prometheus-url]
[![Grafana][Grafana]][Grafana-url]
[![JMeter][JMeter]][JMeter-url]
[![Docker Compose][DockerCompose]][DockerCompose-url]

<!-- Badge 이미지 링크 -->
[Gateway]: https://img.shields.io/badge/Spring%20Cloud%20Gateway-00BFFF?style=for-the-badge&logo=spring&logoColor=white
[Config]: https://img.shields.io/badge/Spring%20Cloud%20Config-7F52FF?style=for-the-badge&logo=spring&logoColor=white
[Eureka]: https://img.shields.io/badge/Eureka%20Server-6DB33F?style=for-the-badge&logo=spring&logoColor=white
[Prometheus]: https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white
[Grafana]: https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white
[JMeter]: https://img.shields.io/badge/JMeter-D22128?style=for-the-badge&logo=apache-jmeter&logoColor=white  
[DockerCompose]: https://img.shields.io/badge/Docker%20Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white
[GitHubConfigRepo]: https://img.shields.io/badge/GitHub%20Config%20Repo-181717?style=for-the-badge&logo=github&logoColor=white

<!-- 웹사이트 링크 -->
[Gateway-url]: https://spring.io/projects/spring-cloud-gateway
[Config-url]: https://spring.io/projects/spring-cloud-config
[Eureka-url]: https://spring.io/projects/spring-cloud-netflix
[Prometheus-url]: https://prometheus.io/
[Grafana-url]: https://grafana.com/
[JMeter-url]: https://jmeter.apache.org/
[DockerCompose-url]: https://docs.docker.com/compose/
[GitHubConfigRepo-url]: https://github.com/

## 배경

- **부하 테스트 중 비정상 종료 패턴이 반복**되어, **단일 Spring Boot 인스턴스의 CPU·메모리 자원 및 스레드 회전율에 병목 가능성**을 가설로 설정하고, **관측 기반 개선**의 필요를 확인  
- **모놀리식은 트래픽이 특정 기능에 몰리면 전체 서비스를 복제**해야 해 비용이 비효율적인 관계로, **구조 확장과 선택적 스케일 아웃이 인프라 비용 효율화 방안**이라 판단  

<br>

## 해결 방법

1. Spring Cloud 기반 **MSA 구조로 전환**하여 서비스 **확장성 확보**
2. **모니터링 기반 부하 테스트**로 병목 원인 규명
    - **Prometheus + Grafana**로 핵심 지표 실시간 수집
    - 테스트 조건 최적화: **Duration + Loop Infinite 방식**에서 **Waiting Thread(app) 급증 현상** 발견
    - 테스트 방식 개선: **Loop Finite 조건으로 변경**하여 현실적 **순간 부하 테스트 환경** 구축
    - **성능 한계점 도출**: Thread 1500 (user) / Ramp-up 5초 / Loop 5회 조건에서 TPS 2,284, P99 639ms 달성
    - 한계 판단 근거: **P99 응답시간 70ms → 639ms 급증**으로 **사용자 체감 성능 저하 구간 확인** (**스레드 회전율(app) 저하**)
3. **로드밸런싱**을 통한 확장성 검증
    - Docker Compose + Eureka + Gateway 기반 다중 인스턴스 로드밸런싱 구현
    - **스레드 회전율(app) 분산**을 통한 성능 개선 시도
    - 로컬 메모리 97% 도달로 **하드웨어적 한계 확인**



<br>

## 변경된 시스템 구조

| 분류 | 도입 기술 | 설명 |
|------|-----------|------|
| 아키텍처 전환 | MSA 구조 | 단일 서버 → 서비스별 독립 배포 및 확장성 확보 |
| API 라우팅 | Spring Cloud Gateway | 외부 요청을 내부 서비스로 분기 |
| 서비스 탐색 | Eureka Server | 서비스 자동 등록 및 탐색 (디스커버리) |
| 설정 관리 | Spring Cloud Config | GitHub 기반 환경 설정 중앙 관리 |
| 설정 저장소 | Config Repo (GitHub) | 서비스별 환경 설정 일원화 및 버전 관리 |
| 성능 모니터링 | Prometheus + Grafana | 메트릭 수집 및 실시간 시각화 대시보드 구성 |



<br>


## 🔥프로젝트 경험

<h2 align="center"> 1. 모니터링 기반 Spring 서버 성능 병목 분석 및 한계 테스트 </h2>

### 1.1 한계 조건 스펙
- TPS 2284 / 99% 600ms / 7500건 5초 처리 기준
- TPS 증가했지만 **99% 응답시간 600ms 도달 및 Thread Waiting 급증 → 성능 한계 도달**

| 구분                 | Threads | Ramp-up | Loop | TPS         | 99% 응답시간 |
|----------------------|---------|---------|------|-------------|--------------|
| Create Thread 1 Group | 750     | 20s     | 3    |**111.0 / sec**  | **624ms**    |
| Create Thread 2 Group | 750     | 20s     | 3    | **111.1 / sec**| **597ms**    |
| Read Thread 1 Group   | 1500    | 5s      | 5    | **2284.8 / sec** |**639ms**   |


-  관측 지표
    - Thread 수: **최대 170개 사용**
    - Thread Waiting: **최대 150개**
    - DB 커넥션: **최대 25개 사용**
    - DB, App **CPU ≤10%**
    - 서버 리소스: **메모리 ≤20%, 디스크 ≤50%**



### 1.2 단계별 성능 분석표(조회 기준)
| **구분** | **조건** | **TPS** | **99% 응답시간** | **상태** |
| --- | --- | --- | --- | --- |
| 테스트 조건 수정 전 | Thread 120 / 60s / Loop ∞ / Duration 60s | **27.3 / sec** | **10660ms** | 성능 심각 저하 |
| 테스트 조건 수정 후 (정상) | Thread 1000 / 5s / Loop 5 | **1929.4 / sec** | **70ms** | 정상 처리 |
| 한계점 도달 | Thread 1500 / 5s / Loop 5 | **2284.8 / sec** | **639ms** | 성능 저하 |


  -한계점 진단
    - **Thread Waiting 패턴 분석**
    - 조건 수정 전: 최대 204개 → 조건 수정 후: 최대 75개
    - 한계 테스트 시: 150개 (부하 증가에 따른 상승)
    
  - **리소스 모니터링**
    - 모든 테스트 조건에서 App, DB 리소스(CPU, 메모리, 디스크) 여유 상태 확인

### 1.3 결론
 - Thread Waiting 패턴으로 **스레드 회전율 병목** 확인

<br>

> 🔗 **성능 개선 과정 전체 정리는 [여기](https://root-property-469.notion.site/MSA-23243673dd8c8058a8d4db459056a6f6?source=copy_link)에서 확인할 수 있습니다.**

<br>
<h2 align="center"> 2. 로드밸런싱 구현 → 메모리 97% 점유로 성능 테스트 실패 </h2>

### 2.1 로드밸런싱 구현 완료
- Docker Compose + Eureka + Gateway 구조로 Spring Boot 서버 인스턴스 추가

### 2.2 개발 환경 한계
- **스레드 회전율(app) 분산**을 통한 성능 개선 시도
- 로컬 메모리 97% 도달로 **하드웨어적 한계 확인**

<br>

> 🔗 **로드밸런싱 구현 과정 전체 정리는 [여기](https://root-property-469.notion.site/23443673dd8c802392e1ee3cb37d73be?source=copy_link)에서 확인할 수 있습니다.**

<br>

<h2 align="center"> 3. Full Table Scan 제거를 통한 성능 개선 (TPS 최대 133% 향상) </h2>

### 3.1 인덱스 최적화

- MSA 구조 전환 후 **Full Table Scan 과다 발생으로 인한 성능 저하** 확인
- API별 복합 인덱스 추가로 Full Table Scan 제거

- **성능 개선 지표**

| API 구분   | 99% 응답시간 (ms) | 개선율    | TPS 개선율 |
|------------|-------------------|-----------|------------|
| 재생 API   | 18,500 → 10,000    | 46% 감소  | 122% 증가  |
| 정지 API   | 18,000 → 10,000    | 44% 감소  | 122% 증가  |
| 조회 API   | 10,000 → 5,000     | 50% 감소  | 133% 증가  |

- **주요 개선 포인트**
  - 복합 인덱스 적용
  - Docker 환경에서 **단일 SSD를 공유**하는 구조적 특성으로 인해, Full Table Scan 제거 → Disk IO 병목 해소 → **전체 서비스 성능 동시 개선**
 
    
<br>

> 🔗 **성능 개선 과정 전체 정리는 [여기](https://root-property-469.notion.site/MSA-Full-Table-Scan-22c43673dd8c80858110e4293bd92659?source=copy_link)에서 확인할 수 있습니다.**


<br>

<!--## 🔫 트러블 슈팅 -->


<br>

<!-- 🔎기술적 의사결정 -->


<br>



## 🏗 아키텍쳐

전체 흐름)

<img width="593" alt="설계도 이미지"  src="https://github.com/user-attachments/assets/f7495088-b122-41d2-b543-07aea65b3d29" />



config 흐름)

<img width="593" alt="설계도 이미지" src="https://github.com/user-attachments/assets/8c6e7843-00df-4c9f-a7b3-20557ebc17af" />

<br>

 
## :bookmark: API 문서

[![Postman](https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)](https://documenter.getpostman.com/view/30989395/2sB34eJ2HM)[![API Documentation](https://img.shields.io/badge/API%20Documentation-6B7280?style=for-the-badge&logo=book&logoColor=white)](https://documenter.getpostman.com/view/30989395/2sB34eJ2HM)


<br>


## 🌐 서버별 API 문서 링크

| 서비스명               | 문서 링크 |
|------------------------|-----------|
| 🌉 Gateway Service      | [GitHub Repo](https://github.com/lee411806/settlement-SCG) |
| 🔎 Eureka Service       | [GitHub Repo](https://github.com/lee411806/settlement-eurekaServer) |
| ⚙️ Config Server        | [GitHub Repo](https://github.com/lee411806/settlement-configServer) |
| 🗂️ Config Repo (GitHub) | [GitHub Repo](https://github.com/lee411806/settlement-config-repo) |
| 📥 Streaming Service    | [GitHub Repo](https://github.com/lee411806/settlement-streaming-msa) |
| 📤 Statistics Service   | [GitHub Repo](https://github.com/lee411806/settlement-statistics-msa) |



