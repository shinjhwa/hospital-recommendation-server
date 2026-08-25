# 🏥 Hospital Recommendation Server

> **외국인 유학생 및 내국인을 위한 AI 기반 위치 맞춤형 병원 추천 및 의약품 검색 비동기 백엔드 서버**

> 본 프로젝트는 2026년 1월 수도권 대학 연합 해커톤(ASCIIthon)에서 장려상을 수상한 서비스의 파이널 데모 버전 백엔드 저장소입니다.

<br/>

## 🌟 Project Overview
외국인 유학생들이 한국에서 아플 때, 언어 장벽과 복잡한 의료 시스템(의료보험 체계, 진료과 분류)으로 인해 적절한 시기에 병원을 방문하지 못하는 문제를 해결하기 위해 기획되었습니다. 

사용자가 겪고 있는 증상을 자연어로 입력하면, **AI가 증상을 분석하여 진료과 및 응급도를 판단**하고, 사용자의 **실시간 GPS 좌표를 기반으로 반경 내 실제 병원을 카카오맵과 연동하여 추천**합니다. 더불어 다국어 의약품 검색 및 동일 성분 대체 약품 추천 기능을 제공하여 의료 접근성을 극대화했습니다.

<br/>

## 🛠️ Tech Stack & Architecture

### Backend Framework
* **FastAPI**: 비동기(Asynchronous) 요청 처리를 통해 외부 API 연동 시의 병목 현상을 최소화하고 고성능 라우팅 구현.
* **Uvicorn**: ASGI 서버를 통한 경량 및 고속 서빙 환경 구축.

### AI & External APIs
* **OpenAI GPT-4o-mini**: 사용자 증상 분석, 맞춤형 예진 차트 자동 생성, 보험 정보 기반 진료비 가이드 라인 및 대체 의약품 추천 엔진으로 활용.
* **Kakao Map Local API**: REST API를 활용하여 사용자의 실시간 GPS 좌표 및 AI가 추천한 진료과 기반의 반경 내 실제 의료기관 정밀 검색.
* **SerpAPI (Google Images)**: 의약품 검색 시 실제 약품 패키지 이미지를 실시간 쿼리하여 시각적 데이터 자동 매핑.

### Infrastructure & DevOps
* **Render.com**: 클라우드 웹 서비스 배포 및 GitHub 연동 자동 배포(CI/CD) 파이프라인 구축.
* **In-Memory Storage**: 데모 버전의 가볍고 빠른 데이터 처리를 위한 메모리 기반 데이터 관리.

<br/>

## 📋 Core API Specifications

| 번호 | 메서드 | 엔드포인트 | 설명 | 핵심 연동 기술 |
| :--- | :---: | :--- | :--- | :--- |
| 1 | `POST` | `/api/signup` | 사용자 회원가입 처리 (기저질환, 알러지, 보험정보 등록) | In-Memory DB 저장 |
| 2 | `POST` | `/api/login` | 사용자 로그인 및 세션 검증 | - |
| 3 | `POST` | `/api/generate-chart` | AI 기반 의료 예진표(Medical History Taking) 실시간 생성 | OpenAI GPT-4o-mini |
| 4 | `POST` | `/api/save-chart` | 사용자가 수정한 최종 예진 차트를 히스토리에 저장 | 데이터 영속화 |
| 5 | `POST` | `/api/estimate-cost` | 유저 보험 정보(NHIS/Private) 분석 후 예상 진료비 안내 | AI Cost Estimation |
| 6 | `POST` | `/api/recommend-hospitals` | **[핵심]** GPS 기반 반경 내 최적 진료과 병원 추천 | KakaoMap API + GPT-4o-mini |
| 7 | `POST` | `/api/search-medicine` | 약품명/증상 검색, 한·영 이중언어 정보 및 동일 주성분 약 추천 | SerpAPI + GPT-4o-mini |
| 8 | `POST` | `/api/update-user` | 회원 정보 및 의료 데이터(보험, 주소 등) 수정 | - |
| 9 | `GET` | `/api/history/{user_id}` | 과거 예진 차트 저장 기록 및 히스토리 조회 | - |
| 10 | `GET` | `/api/users` | [Debug 전용] 전체 사용자 데이터 리스트업 | 디버깅 및 모니터링 |

<br/>

## 🚀 System Architecture Flow
1. **증상 입력 및 좌표 수집**: 프론트엔드에서 사용자의 GPS 좌표와 자연어 증상 획득 후 백엔드로 전송.
2. **AI 도메인 분석**: `GPT-4o-mini` 가 증상을 매핑하여 최적의 `추천 진료과`와 `응급도`를 판별하고 한/영 추천 사유 도출.
3. **위치 기반 필터링**: AI가 도출한 진료과 키워드와 유저 좌표를 활용하여 `KakaoMap API` 쿼리, 반경 내 5개 최적 병원 정렬.
4. **의약품 및 이미지 융합**: 약품 검색 시 AI가 동일 주성분 대체 약품을 서칭함과 동시에 `SerpAPI` 가 패키지 이미지를 크롤링하여 결합 후 응답.

<br/>

## 🔒 Security & Production Roadmap
* **비밀번호 보안 강화**: 현재 Plain Text 저장 방식을 `bcrypt` 단방향 암호화 해싱 적용 예정.
* **인증 아키텍처 도입**: 무인증 구조에서 `JWT(JSON Web Token)` 기반 Bearer 인증 시스템 도입 예정.
* **CORS 및 환경변수 보호**: 프로덕션 배포 시 특정 프론트엔드 도메인만 허용하도록 제한하고, API Key 그룹은 AWS Secret Manager 혹은 GCP Secret Manager로 이관 계획.
