## 프론트엔드 배포 파이프 라인

> Chapter 4-1. **인프라 관점의 성능 최적화**

### 개요

1. GitHub에 Next.js Repository 생성
2. AWS S3 Bucket 생성 및 static web hosting 설정
3. CloudFront 배포 생성 및 S3 Bucket 연결
4. IAM 사용자 생성 및 액세스 키 발급
5. GitHub 저장소에 Secret 값 등록
6. GitHub Actions workflow 작성
7. `main` branch에 push시 자동으로 배포로 설정

### 주요 링크

- S3 버킷 웹사이트 엔드포인트: http://hanghae-donghyun.s3-website-ap-southeast-2.amazonaws.com/
- CloudFrount 배포 도메인 이름: https://d354cw4mmho3tl.cloudfront.net/

### 주요 개념

- GitHub Actions과 CI/CD 도구: 코드를 push할 때 자동으로 빌드/배포해주는 자동화 도구입니다.
- S3와 스토리지: 정적 웹사이트를 호스팅할 수 있는 AWS의 객체 스토리지 서비스입니다.
- 버킷: S3에서 데이터를 저장하는 기본 단위
- CloudFront와 CDN: 전 세계 사용자에게 빠르게 정적 리소스를 제공하기 위한 콘텐츠 전송 네트워크입니다.
- 캐시 무효화(Cache Invalidation): CloudFront가 이전 버전의 파일을 캐싱하지 않도록 모든 파일을 새로 불러오게 합니다.
- Repository secret과 환경변수: 액세스 키와 같은 민감한 정보를 GitHub Actions에서 안전하게 사용할 수 있도록 저장하는 설정입니다.

---

### CDN과 성능최적화

CDN(Content Delivery Network)은 사용자에게 지리적으로 가까운 서버에서 정적 파일을 제공함으로써 **응답 속도를 단축**하고 **트래픽 부하를 분산**시킵니다. CloudFront는 AWS의
CDN 서비스로, S3에 있는 정적 리소스를 전 세계 사용자에게 빠르게 전달할 수 있도록 해줍니다.

이해를 위해서 피자 서비스를 예를 드는데,
일반적인 웹사이트는 하나의 가게에서 전량 주문을 처리하고 배달하는데, CDN 사용 웹사이트는 도시 곳곳 지점을 놓고 배달 요청이 들어오면 주문지에서 제일 가까운 곳에서 배달을 하는것.

가장 가까운 지점 (CDN의 Edge server)에서 피자 (Contents)를 배달하고, 요청한 피자가 없다면, 중앙 주방 (원본 서버)에서 가져와서 배달하고, 다음을 대비해 그 지점에 해당 피자를 보관 (캐싱)하는것.

빠른 배달로 낮은 지연 시간이 있고, 동시에 주문을 처리할 수있고 (높은 동시성), 한 곳에 문제가 생기면 다른 가까운 지점에서 배달 가능하다 (높음 가용성)

물론 비용적으로는 배달된 피자의 양 (전송된 데이터)와 주문 횟수(요청 수)에 따라 비용이 청구됨.

-