# Cocokalo 애니메이션 플랫폼

![Cocokalo 로고](public/images/project-logo.png)

#### 설명
Bilibili에서 영감을 받은 UI 디자인의 오픈 소스 애니메이션 비디오 플랫폼입니다. 학습 및 연구 목적으로만 사용하십시오.

#### 저작권 공지
1. 이는 상업적 목적이 없는 오픈 소스 학습 프로젝트입니다
2. Cocokalo를 비학술 활동에 사용하는 것은 엄격히 금지됩니다
3. 이 프로젝트에서 사용된 모든 애니메이션 콘텐츠는 원작자에게 귀속됩니다
4. 무단 저작권 보호 콘텐츠를 저장하거나 배포하기 위해 이 프로젝트를 사용하지 마십시오

#### 라이선스
이 프로젝트는 GNU Affero General Public License 3.0으로 라이선스되어 있습니다. 전체 조건은 [LICENSE](LICENSE) 파일을 참조하십시오. 이 라이선스에 따라:
1. 모든 사용, 수정 또는 배포는 이 라이선스를 준수해야 합니다
2. 네트워크를 통해 서비스를 제공할 때 소스 코드를 사용할 수 있도록 해야 합니다
3. 모든 수정 사항은 명확하게 표시되고 원본 저작권 공지를 유지해야 합니다
4. 허가 없이 프로젝트 이름을 홍보에 사용하지 마십시오

## 기술 스택
- 백엔드 프레임워크: Express 4.x
- 템플릿 엔진: EJS 3.x
- 데이터베이스: MongoDB >=7.x
- 패키지 관리자: npm 9.x

## 빠른 시작
```bash
# 종속성 설치
npm install

# 개발 모드로 시작 (핫 리로드 포함)
npm run dev

# 프로덕션 모드로 시작
npm start

# 데이터베이스 초기화
node scripts/initDB.js
```

## 프로젝트 구조 (MVC 아키텍처)
```
├── app.js         # 애플리케이션 진입점
├── models/        # 데이터 모델 (M)
├── routes/        # 라우트 컨트롤러 (C)
├── views/         # 뷰 템플릿 (V)
├── public/        # 정적 리소스
├── scripts/       # 데이터베이스 마이그레이션 스크립트
└── config/        # 환경 구성
```

## 환경 구성
.env 파일 생성:
```ini
PORT=3000
MONGODB_URI=mongodb://localhost:27017/cocokalo
SESSION_SECRET=your_session_secret
```

## API 문서
`/api-docs`에서 Swagger 문서를 확인하십시오