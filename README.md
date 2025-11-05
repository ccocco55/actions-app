# 🚀 Single EC2 Deployment Architecture (CI/CD + AWS)

## 🧩 개요
본 서비스는 단일 **AWS EC2(Spring1)** 인스턴스에서  
**Docker 기반 Spring Boot 애플리케이션**을 배포·운영하는 구조로 설계되었습니다.  
CI/CD 자동화는 **GitHub Actions**를 이용하며,  
배포 시 필요한 민감한 정보는 **Git-Secret**을 통해 안전하게 관리됩니다.

---

## ☁️ 전체 아키텍처 구성도
![슬라이드4](https://github.com/user-attachments/assets/351eeb0c-34bd-4e46-8f76-927419787ef0)

---

## ⚙️ 구성 요소

| 구성 요소 | 역할 |
|------------|------|
| **EC2 (Spring1)** | Docker 기반 Spring Boot 앱 실행 환경 |
| **PostgreSQL** | 주요 데이터 저장소 |
| **Redis** | 세션 및 캐시 데이터 관리 |
| **Amazon S3** | 정적 파일(이미지 등) 저장 |
| **AWS IAM** | S3 접근 및 권한 관리 |
| **GitHub Actions** | 자동 배포 및 빌드 파이프라인 |
| **Git-Secret** | AWS 및 DB 인증 정보 보안 관리 |
| **Dockerfile** | 컨테이너 빌드 및 실행 정의 |
| **deploy.yml** | CI/CD 자동화 스크립트 정의 |

---

## 🔄 배포 및 동작 흐름

1. 개발자가 로컬 환경에서 코드를 수정 후 **GitHub에 Push**  
2. `GitHub Actions`가 트리거되어 `deploy.yml` 스크립트를 실행  
3. Docker 이미지를 빌드하고, EC2 인스턴스로 전송 및 컨테이너 실행  
4. 애플리케이션은 내부적으로 **Spring Security**, **JWT**를 사용하여 인증/인가 처리  
5. 정적 파일은 **Amazon S3**, 캐시 데이터는 **Redis**, 영속 데이터는 **PostgreSQL**과 연동  
6. 클라이언트(Web/React Native)가 EC2의 Public IP 또는 도메인을 통해 API 호출

---

## 🔧 CI/CD 구성 요소

### GitHub Actions (`.github/workflows/deploy.yml`)
- 코드 푸시 시 자동 실행  
- Docker 이미지 빌드 → EC2 SSH 접속 → 컨테이너 재배포  
- 환경 변수는 Git-Secret으로 관리  

### Git-Secret
- AWS IAM Key, DB Credentials 등 민감 정보 암호화  
- GitHub Secrets에 등록된 값으로 `deploy.yml` 실행 시 자동 주입  

### Dockerfile
- Spring Boot JAR 파일을 컨테이너 이미지로 패키징  
- 경량 이미지로 빌드 (예: `openjdk:17-jdk-slim`)  
- 내부 포트 8080 노출  

---

## 🧱 트래픽 흐름

1. 클라이언트(Web 또는 React Native) → EC2(Spring1) 요청  
2. Spring Boot 서버 → PostgreSQL, Redis, S3 연동  
3. 응답을 클라이언트로 반환  


---

## 🧩 확장성 및 보안 고려사항

- **확장**: EC2 인스턴스를 복제하여 Nginx 로드밸런싱 구조로 전환 가능  
- **보안**:  
  - EC2 인바운드 최소 포트(22, 80, 5432, 6379)만 허용  
  - 환경 변수는 Git-Secret으로 관리  
- **백업**:  
  - PostgreSQL 데이터는 정기 백업 또는 Amazon RDS 마이그레이션 권장  
  - Redis는 캐시 용도이므로 영구 저장 불필요  

---

## 🛠️ 기술 스택

| 영역 | 기술 |
|------|------|
| Infra | AWS EC2, S3, IAM |
| Backend | Spring Boot, Spring Security, JWT |
| Database | PostgreSQL |
| Cache | Redis |
| CI/CD | GitHub Actions, Docker, Git-Secret |
| Client | Web (Chrome), React Native |

---

## 📈 기대 효과

- ✅ CI/CD 자동화를 통한 **빠르고 일관된 배포 프로세스**  
- ✅ 단일 EC2로 **운영 비용 절감**  
- ✅ Docker 기반 환경으로 **개발/운영 환경 일치성 확보**  
- ⚠️ 추후 트래픽 증가 시 **로드밸런서 및 멀티 EC2 확장 용이**

