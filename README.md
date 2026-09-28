# HeyInsole Kids

**걸음으로 보는 아이 발달·성장 모니터링** · HeyInsole by NEWDIC

HeyInsole 스마트 인솔(FSR 8채널 + 9축 IMU, 100 Hz)의 걸음 데이터를 아이의 나이·성별·키에 맞는 또래 기준과 비교해, 보행 · 뇌·운동 발달 · 키 성장 · 성장 관리 네 영역으로 보여주는 웹앱입니다.

- 웹앱: `https://heyinsole-kids.web.app` (Firebase Hosting 배포 후)
- 근거 검토 자료: `/evidence`
- 알고리즘 기술 리포트(Word): `/docs/HeyInsole_v2_algorithm_report.docx`

> 현재 공개 버전은 예시 데이터(아이 4명)로 동작하는 데모입니다. 선별·관리 목적이며 의학적 진단 도구가 아닙니다.

---

## 주요 화면

| 탭 | 내용 |
|---|---|
| 대시보드 | HeyInsole 점수와 4개 영역 점수, 영역별 근거 문헌, 형제자매 한눈에 비교 |
| 걸음 라이브 | 알고리즘이 계산한 보폭·발 각도로 움직이는 발자국 애니메이션, 보행 주기 타임라인, 센서 파형, 넘어짐 4단계 감지 |
| 발달 리포트 | 결과지 형식: 보행 분석, 발바닥 하중·자세, 좌우 균형, 성장 분석, 변화 추이, 보행 성숙 연령, 생활 가이드 |
| 성장 관리 | 키·체중 성장곡선(2017 소아청소년 성장도표 기준), BMI, 성장 속도, 부모 기반 목표키, 발 성장·인솔 교체, 14일 활동량 |
| HeyInsole 알고리즘 | 6개 분석 모듈, 처리 흐름, 근거 기반 설계표, 참고문헌 |
| 성능 검증 리포트 | 신호 모델 기반 사전 검증 결과 |

## HeyInsole v2 알고리즘

| 모듈 | 내용 |
|---|---|
| 01 체중 적응형 접지 검출 | 본인 최대 하중의 12%에서 딛음, 7%에서 떼기. 15 kg 유아부터 성인까지 같은 방식 |
| 02 자세 보정 보폭 추정 | 자이로 피치 적분으로 중력 제거, 영속도 보정(ZUPT)과 선형 드리프트 제거 |
| 03 발진행각 | 유각기 변위 방향으로 안짱(−)·팔자(+) 각도 산출 |
| 04 발바닥 하중 분석 | 뒤꿈치·중족부·앞발 분담률, 초기 접지 부위 → 발끝 보행·생리적 평발 구분 |
| 05 연령 표준과 점수 | 3~17세 z-점수, 보행 성숙 연령, 4개 영역 점수 |
| 06 넘어짐 4단계 판정 | 충격 → 자세 변화 → 하중 소실 → 무동작 |

신호 모델 검증(5명, 체중 16~72 kg) 결과: 걸음 검출률 98.8%, 보폭 오차 0.17%, 발진행각 오차 0.02°. 실제 인솔 실측 검증 전 수치이며, 실측 검증(타당도 → 선별 정확도 → 효과 연구)을 진행할 예정입니다.

## 근거와 표현 원칙

| 주장 | 근거 수준 |
|---|---|
| 걷기·뛰기 활동량이 성장기 뼈 건강에 도움 | 강함 (WHO 2020, 메타분석) |
| 걸음 리듬·발끝 보행·안짱걸음 추적으로 진료 시점 안내 | 중간 |
| 통증 없는 평발·안짱걸음은 대부분 자연 경과 | 중간 (Cochrane 2022 등) |
| 걸음 교정·운동으로 키가 더 큼 | 근거 없음 → 주장하지 않음 |

HeyInsole Kids는 뇌 발달이나 키를 직접 측정하거나 키우지 않습니다. 운동 발달 신호, 성장 속도, 활동량을 매일 측정해 또래 기준과 비교하고 확인이 필요한 시점을 알려 줍니다.

## 저장소 구조

```
public/
  index.html              웹앱 (단일 파일)
  evidence.html           근거 검토 자료
  docs/                   알고리즘 기술 리포트(.docx)
  manifest.webmanifest    홈 화면 설치(PWA)용 정보
  icon.svg, icon-192.png, icon-512.png
  404.html
firebase.json             Firebase Hosting 설정
.firebaserc               Firebase 프로젝트 ID (heyinsole-kids)
.github/workflows/        main에 push하면 자동 배포
```

## 배포 방법

### 처음 한 번 (내 컴퓨터에서)

```bash
npm install -g firebase-tools
firebase login
firebase projects:create heyinsole-kids   # 이미 만든 프로젝트가 있으면 생략하고 .firebaserc의 ID를 바꿉니다
firebase deploy --only hosting
```

배포가 끝나면 `https://heyinsole-kids.web.app` 에서 열립니다. 프로젝트 ID가 이미 사용 중이면 다른 ID로 만들고 `.firebaserc`와 워크플로 파일의 `projectId`를 같은 값으로 바꿉니다.

### GitHub에서 자동 배포

```bash
firebase init hosting:github
```

위 명령이 서비스 계정을 만들어 저장소 Secret(`FIREBASE_SERVICE_ACCOUNT_HEYINSOLE_KIDS`)에 등록합니다. 이후 `main`에 push하면 자동 배포되고, Pull Request마다 미리보기 URL이 생깁니다. 이미 들어 있는 `.github/workflows/firebase-hosting.yml`을 덮어쓰라고 물으면 그대로 두거나 새 파일로 교체해도 됩니다.

## 라이선스와 고지

© 2026 NEWDIC Co., Ltd. All rights reserved.
예시 데이터와 신호 모델 결과는 실제 사용자 데이터가 아닙니다. 판정은 선별 목적이며 적색 항목은 소아청소년과·소아정형외과 상담을 권장합니다.
