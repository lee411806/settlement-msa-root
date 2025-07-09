# 정산 시스템 MSA 프로젝트

📅 <b>2025 ~ , 보완 중 </b> | (1명)



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

## 프로젝트 목표
1. **MSA 구조로 전환하여 서비스 확장성 확보**  <br>
2. **JMeter 테스트에서 발생한 병목 문제를 해결하고 테스트를 안정적으로 마무리**  <br>
3. **CPU 과부하 상황에 대응하기 위한 로드밸런싱 구조 구현**  <br>
4. **APM 도구 연동을 통한 병목 지점 모니터링 및 지속적인 성능 개선**  <br>
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


<br>

## 🔫 트러블 슈팅

<br>

## 🔎기술적 의사결정


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



