# 🎯 Lotto Number Recommendation System (로또 번호 추천 시스템)

## 📌 프로젝트 개요
이 프로젝트는 6개의 독립적인 서브도메인(API 시스템)과 공통 모듈을 포함하는 **로또 번호 추천 시스템**입니다.  
각 서브도메인은 개별적인 추천 알고리즘을 사용하며, `number_check`를 통해 추천 번호의 **당첨 여부를 관리**합니다.  
전체 시스템은 `common/` 폴더를 활용하여 코드의 중복을 최소화하고, `scripts/` 폴더에서 자동 실행을 제어합니다.

---

## 📂 디렉토리 구조 및 파일 설명
```
/var/www/lotto_number/
│
├── common/                        # 공통 기능 폴더 (모든 서비스에서 공유)
│   ├── generate_all_lotto_numbers.py  # 814만 개 로또 번호 생성 (1회 실행 후 유지)
│   ├── get_lotto_number.py            # 중복 없는 로또 번호 추천 API
│   ├── reset_lotto_weekly.py          # 매주 추천 번호 초기화 (cron 사용)
│   ├── auth.py                         # 로그인 & 인증 처리 (JWT)
│   ├── database.py                     # MariaDB 연결 모듈 (SQLAlchemy)
│   ├── utils.py                         # 공통 유틸리티 함수 (날짜 변환, 로깅 등)
│   ├── config.py                        # 공통 환경설정 (DB 정보, API 키 등)
│   ├── cache.py                         # Redis 캐시 핸들링 모듈 (번호 저장용)
│   ├── api.py                           # API 공통 엔드포인트 정의
│   ├── middleware.py                    # 공통 미들웨어 (에러 핸들링, 로깅 등)
│
├── toktok/                         # 허수배제진수추출기법 기반 추천 시스템
│   ├── app/
│   │   ├── static/                     # 정적 파일 (CSS, JS, 이미지)
│   │   ├── templates/                  # HTML 템플릿 파일
│   │   │   ├── login.html                # 로그인 화면
│   │   │   ├── register.html             # 회원가입 화면
│   │   │   ├── transition.html           # 트랜지션 화면 (추천번호 액션 필요)
│   │   │   ├── home.html                 # 홈 화면 (추천 번호 확인)
│   │   │   ├── forgot_password.html      # 비밀번호 찾기 화면
│   │   │   ├── reset_password.html       # 비밀번호 재설정 화면
│   │   │   ├── view_code.html            # 코드번호 보기 화면 (유튜브 연동)
│   │   ├── routes.py                    # Flask API (추천 번호 제공)
│   │   ├── models.py                    # 데이터베이스 모델
│   │   ├── toktok_recommend.py          # 허수배제진수추출 추천 로직
│   │   ├── config.py                    # 환경설정 (DB 연결 정보 포함)
│   │   └── run.py                        # 실행 파일 (Flask API 실행)
│
├── ai/ ... (billion, fortune, free, tarot 동일한 구조) ...
│
├── number_check/                    # 추천 번호 및 당첨 결과 통합 관리 시스템
│   ├── app/
│   │   ├── static/
│   │   ├── templates/
│   │   ├── routes.py                   # Flask 라우트 (관리자 페이지)
│   │   ├── models.py                   # 당첨 비교 DB 모델
│   │   ├── services.py                 # 당첨 번호 확인 로직
│   │   ├── auth.py                     # 관리자 로그인 처리
│   │   ├── config.py                   # 환경설정 (DB 정보 포함)
│   │   ├── db_migration.py             # DB 마이그레이션 스크립트
│   │   ├── run.py                      # 실행 파일 (Flask API 실행)
│   │   ├── requirements.txt            # Python 패키지 목록
│
├── scripts/                         # 자동 실행 & 관리 스크립트
│   ├── start_services.sh             # 모든 서브도메인 API 실행 (nohup 사용)
│   ├── stop_services.sh              # 모든 API 중지
│   ├── restart_services.sh           # API 전체 재시작
│   ├── db_backup.sh                   # 데이터베이스 백업 스크립트
│   ├── update_subdomains.sh           # 서브도메인 업데이트 스크립트
│   ├── deploy.sh                      # 배포 자동화 스크립트
│
├── database/                         # 데이터베이스 및 백업 관리
│   ├── migrations/                   # 데이터베이스 마이그레이션 파일
│   ├── backup/                        # 백업 파일 저장소 (자동화 가능)
│   ├── init.sql                       # 초기 DB 스키마 파일
│   ├── db_setup.sh                    # DB 초기화 스크립트
│
├── venv/                             # Python 가상환경 (각 서비스에서 공용 사용)
│
└── README.md                         # 프로젝트 설명 문서
```

---

## 📌 API 엔드포인트
| 서브도메인 | API 엔드포인트 | 설명 |
|------------|--------------|------|
| `toktok`   | `/get_number` | 허수배제 진수추출기법 로또 추천 번호 제공 |
| `number_check` | `/get_numbers` | 모든 서브도메인의 추천 번호 조회 |
| `ai`       | `/get_number` | AI 기반 로또 번호 추천 |
| `billion`  | `/get_number` | 빅데이터 기반 추천 |
| `fortune`  | `/get_number` | 사주 기반 추천 |
| `free`     | `/get_number` | 패턴 기반 추천 번호 (유료회원 유입 확대) |
| `tarot`    | `/get_number` | 타로, 별자리 기반 추천 |

---

## 📌 회원 시스템
- **로그인 페이지**
  - 아이디, 비밀번호, 코드번호 입력
  - 회원가입, 비밀번호 찾기, 코드번호 보기 링크 제공

- **회원가입 페이지**
  - 아이디, 이메일, 비밀번호, 비밀번호 확인 입력

- **비밀번호 찾기**
  - 이메일을 통한 비밀번호 재설정 제공

- **코드번호 보기**
  - 유튜브 쇼츠 영상을 통해 코드번호 확인 가능

---

✅ **이 문서를 유지보수하면서 프로젝트를 관리하세요! 🚀🔥**



현재 README.md는 프로젝트의 구조와 기능을 명확하게 설명하고 있어. 하지만 추가적으로 보강하면 좋을 내용이 있다면 다음 사항을 고려해 볼 수 있어:

트랜지션 페이지의 액션 방식 상세화

서브도메인별로 트랜지션의 차별점을 어떻게 둘 것인지 간단히 설명 추가
예를 들어 AI 기반 추천은 버튼을 눌러 AI가 예측하는 방식, 타로 기반 추천은 카드를 선택하는 방식 등
유료회원과 무료회원 구분 방식

현재는 유료회원 정책이 나중에 적용될 예정인데, 어떤 방식으로 구별할 예정인지 설명 보강 가능
예를 들어, 데이터베이스에서 membership_type 필드를 두어 free와 premium으로 관리하는 방식
추천 알고리즘의 설명 추가

toktok의 허수배제진수추출기법은 간략히 설명되어 있지만, 다른 서브도메인의 추천 방식에 대한 간단한 설명이 추가되면 더 좋을 것 같아.
보안 및 인증 방식 설명

auth.py에서 JWT 기반 로그인 처리를 하는 것으로 보이는데, 사용자 비밀번호 암호화 방식과 토큰 관리 방식에 대한 설명을 추가하면 좋을 듯
배포 및 운영 관련 정보 추가

deploy.sh를 이용한 배포 과정이 어떻게 이루어지는지 간략히 정리
서버 재시작 및 업데이트 방식 설명
위 내용을 추가하면 README.md가 더욱 완성도 높은 문서가 될 거야.
이 내용 중에서 필요한 부분이 있으면 수정해 줄게! 🚀
