# Git과 SVN(Subversion)의 차이점

## 1. 저장소 구조

### Git
- 분산형 버전 관리 시스템(Distributed VCS)
- 각 개발자가 전체 저장소의 복사본을 로컬에 가짐
- 네트워크 연결 없이도 작업 가능
- 중앙 서버가 다운되어도 로컬에서 계속 작업 가능

### SVN
- 중앙집중식 버전 관리 시스템(Centralized VCS)
- 중앙 서버에 모든 버전 정보가 저장됨
- 작업을 위해 항상 서버 연결 필요
- 서버 장애 시 버전 관리 작업 불가

## 2. 브랜치 관리

### Git
- 가볍고 빠른 브랜치 생성
- 로컬에서 브랜치 생성/관리 가능
- 브랜치 전환이 빠름
- 병합(merge)이 더 유연함
```bash
# Git 브랜치 생성 및 전환
git branch feature-login
git checkout feature-login
# 또는
git checkout -b feature-login
```

### SVN
- 브랜치가 디렉토리 복사 개념
- 서버에서만 브랜치 생성 가능
- 브랜치 전환이 상대적으로 느림
- 병합 과정이 더 복잡할 수 있음
```bash
# SVN 브랜치 생성
svn copy http://svn.example.com/trunk http://svn.example.com/branches/feature-login -m "Creating feature-login branch"
svn switch http://svn.example.com/branches/feature-login
```

## 3. 커밋과 리비전

### Git
- 커밋은 전체 프로젝트의 스냅샷
- SHA-1 해시로 커밋 식별
- 오프라인 상태에서도 커밋 가능
- 로컬 저장소에서 커밋 히스토리 관리
```bash
# Git 커밋
git add .
git commit -m "Add login feature"
git push origin main
```

### SVN
- 각 파일의 변경사항만 저장
- 순차적인 리비전 번호 사용
- 커밋을 위해 서버 연결 필요
- 중앙 서버에서 히스토리 관리
```bash
# SVN 커밋
svn update
svn add new-file.txt
svn commit -m "Add login feature"
```

## 4. 작업 흐름

### Git
- Pull Request 기반 협업
- 로컬에서 작업 후 원격 저장소에 Push
- 다양한 워크플로우 지원 (Git Flow, GitHub Flow 등)
- 병렬 개발이 용이

### SVN
- 트렁크 기반 개발
- 작업 전 항상 Update 필요
- 단순한 워크플로우
- 순차적 개발에 적합

## 5. 장단점 비교

### Git 장점
- 오프라인 작업 가능
- 빠른 브랜칭과 머징
- 다양한 워크플로우 지원
- 강력한 협업 기능

### Git 단점
- 학습 곡선이 높음
- 큰 바이너리 파일 처리가 비효율적
- 복잡한 명령어 체계

### SVN 장점
- 간단한 학습 곡선
- 직관적인 버전 번호
- 큰 바이너리 파일 처리가 효율적
- 접근 권한 관리가 세밀함

### SVN 단점
- 항상 네트워크 연결 필요
- 브랜치/병합이 복잡
- 오프라인 작업 제한
- 백업이 상대적으로 어려움

## 6. 사용 사례

### Git에 적합한 경우
- 분산된 팀 작업
- 병렬적인 기능 개발
- 실험적인 기능 개발
- 오픈 소스 프로젝트

### SVN에 적합한 경우
- 중앙 집중식 관리가 필요한 경우
- 대용량 바이너리 파일이 많은 프로젝트
- 엄격한 접근 제어가 필요한 경우
- 단순한 버전 관리가 필요한 경우
