# front_5th_chapter4-1
## Chapter 4-1. 인프라 관점의 성능 최적화
![image](https://github.com/user-attachments/assets/adf16fd0-475d-4770-9ea7-eb7a1275e0e7)

## 주요 링크

- S3 버킷 웹사이트 엔드포인트: http://yerim-front-5th-chapter4-1.s3-website.ap-northeast-2.amazonaws.com/
- CloudFrount 배포 도메인 이름: https://d3w2u1vgiii1wc.cloudfront.net

## 주요 개념

### GitHub Actions과 CI/CD 도구 

GitHub Actions는 GitHub에서 제공하는 CI/CD(Continuous Integration/Continuous Deployment) 플랫폼으로 코드 변경사항을 자동으로 빌드, 테스트, 배포할 수 있게 해주는 서비스.
YAML 파일로 워크플로우를 정의하며, GitHub 저장소와의 긴밀한 통합이 특징

#### 주요 CI/CD 도구 비교

| 도구 | 장점 | 단점 | 
|------|------|------|
| **GitHub Actions** | GitHub 통합, 무료 제공량, 다양한 액션 마켓플레이스 | GitHub 의존성, 복잡한 워크플로우 제한 |
| **Jenkins** | 오픈소스, 높은 확장성, 플러그인 생태계 | 설정 복잡, 유지보수 부담 |
| **GitLab CI/CD** | GitLab 통합, Docker 지원, Auto DevOps | GitLab 의존성, 러닝커브 |
| **CircleCI** | 빠른 빌드, Docker 지원, 병렬 처리 | 비용, 복잡한 설정 |

### S3와 스토리지

Amazon S3(Simple Storage Service)는 AWS에서 제공하는 객체 스토리지 서비스.
웹 애플리케이션, 백업, 아카이브, 데이터 분석 등 다양한 용도로 사용. 무제한 저장 용량을 지원.

#### 클라우드 스토리지 서비스 비교

| 서비스 | 제공업체 | 특징 | 가격 모델 |
|---------|----------|------|-----------|
| **Amazon S3** | AWS | 높은 내구성, 다양한 스토리지 클래스 | 사용량 기반 과금 |
| **Google Cloud Storage** | Google | BigQuery 통합, 머신러닝 API | 사용량 기반 과금 |
| **Azure Blob Storage** | Microsoft | Azure 생태계 통합, 계층화 스토리지 | 사용량 기반 과금 |
| **Cloudflare R2** | Cloudflare | 무료 이그레스, S3 호환 API | 저렴한 이그레스 비용 |

### CloudFront와 CDN

Amazon CloudFront는 AWS의 글로벌 CDN(Content Delivery Network) 서비스.
전 세계에 분산된 엣지 로케이션을 통해 콘텐츠를 사용자에게 빠르게 제공. 웹사이트 로딩 속도 향상과 서버 부하 감소가 주요 목적.

#### 주요 CDN 서비스 비교

| CDN 서비스 | 제공업체 | 엣지 로케이션 | 주요 특징 |
|------------|----------|---------------|-----------|
| **CloudFront** | AWS | 400+ | AWS 서비스 통합, SSL/TLS 지원 |
| **Cloudflare** | Cloudflare | 330+ | DDoS 보호, 무료 플랜 제공 |
| **Fastly** | Fastly | 60+ | 실시간 캐시 제어, 개발자 친화적 |
| **Google Cloud CDN** | Google | 130+ | Google 네트워크 활용, 머신러닝 최적화 |

### 캐시 무효화(Cache Invalidation)

캐시 무효화는 CDN이나 캐시 서버에 저장된 오래된 콘텐츠를 제거하여 사용자가 최신 버전의 콘텐츠를 받을 수 있도록 하는 프로세스. 웹사이트 업데이트나 콘텐츠 변경 시 필수적인 작업.

#### 캐시 무효화 방법
#### CloudFront 무효화
```bash
# AWS CLI를 통한 무효화
aws cloudfront create-invalidation \
  --distribution-id E123456789 \
  --paths "/*"
```

#### 일반적인 무효화 전략
- **전체 무효화**: 모든 캐시된 콘텐츠 제거 (비용 높음)
- **선택적 무효화**: 특정 파일이나 경로만 무료화
- **버전 관리**: 파일명에 버전 번호나 해시 추가
- **TTL 설정**: 적절한 캐시 만료 시간 설정

#### 무효화 비용 고려사항
- CloudFront: 월 1,000개 무료, 이후 요청당 과금
- Cloudflare: 무료 플랜에서도 무제한 무효화 제공
- 빈번한 무효화는 성능과 비용에 영향

### Repository secret과 환경변수

Repository Secret은 GitHub에서 제공하는 보안 기능.
API 키, 패스워드, 토큰 등의 민감한 정보를 안전하게 저장하고 CI/CD 파이프라인에서 사용할 수 있게 함. 평문으로 노출되지 않으며, 로그에도 마스킹되어 표시.

#### Secret 관리 계층

| 레벨 | 범위 | 사용 목적 |
|------|------|-----------|
| **Repository Secret** | 단일 저장소 | 해당 저장소 전용 설정 |
| **Organization Secret** | 조직 전체 | 여러 저장소 공유 설정 |
| **Environment Secret** | 특정 환경 | 배포 환경별 분리된 설정 |

## CDN과 성능최적화

### S3 vs S3+CloudFront 성능 비교

![image](https://github.com/user-attachments/assets/f7c91abc-2186-44fa-87ef-e31f0fab5c93)

### 전체 성능 비교

| 메트릭 | S3 단독 | S3 + CloudFront | 개선율 |
|--------|---------|-----------------|--------|
| **총 요청 수** | 17 requests | 17 requests | - |
| **전송된 데이터** | 473 kB | 198 kB | **58% 감소** |
| **총 리소스 크기** | 467 kB | 467 kB | - |
| **페이지 로드 완료** | 201 ms | 158 ms | **21% 빨라짐** |
| **DOMContentLoaded** | 97 ms | 66 ms | **32% 빨라짐** |
| **Load 이벤트** | 169 ms | 143 ms | **15% 빨라짐** |

### 주요 개선 사항

#### 데이터 전송량 최적화
- **58% 데이터 전송량 감소** (473kB → 198kB)
- CloudFront의 **Gzip 압축** 및 **캐싱** 효과
- 불필요한 요청 헤더 최적화

#### 로딩 성능 향상
- **DOMContentLoaded 32% 단축** (97ms → 66ms)
- **전체 로드 시간 21% 단축** (201ms → 158ms)
- 엣지 로케이션을 통한 **지리적 지연 시간 감소**

#### 파일별 로딩 시간 개선

| 파일 유형 | S3 평균 로딩 시간 | CloudFront 평균 로딩 시간 | 개선율 |
|-----------|------------------|--------------------------|--------|
| **CSS 파일** | ~25ms | ~8ms | **68% 빨라짐** |
| **JavaScript** | ~30ms | ~10ms | **67% 빨라짐** |
| **이미지(SVG)** | ~22ms | ~12ms | **45% 빨라짐** |
| **폰트 파일** | ~65ms | ~9ms | **86% 빨라짐** |

### CloudFront 도입 효과

#### 장점
- **대역폭 비용 절약**: 58% 데이터 전송량 감소
- **사용자 경험 개선**: 페이지 로딩 속도 향상
- **서버 부하 감소**: 원본 S3 요청 횟수 감소
- **글로벌 배포**: 전 세계 사용자에게 일관된 성능 제공

#### 최적화 요소
- **Gzip/Brotli 압축** 자동 적용
- **HTTP/2** 프로토콜 지원
- **엣지 캐싱**으로 반복 요청 최적화
- **Keep-Alive** 연결 재사용
