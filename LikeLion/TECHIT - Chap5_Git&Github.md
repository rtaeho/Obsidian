### Git & Github의 등장과 역할
 - 개발을 하면서 생성하는 많은 파일들의 관리
 - 다수의 공동 작업의 필요성
 **Git** : 소스 이력 추적을 위한 버전 관리 시스템
 **Github** : git 프로젝트를 관리하는 저장소 제공
 
### Git 설치 및 세팅하기 - 맥
1. 터미널 실행
2. xcode-select --install 입력 후 실행
3. brew 설치
4. git credential manager 설치

### Git 동작 흐름과 구성 요소
- local
	- Working directory (폴더) : 실제 작업하는 공간
	- Staging : commit할 파일의 예비 저장소
	- Local Repository : commit한 파일의 저장소 
			-  언제든지 저장한 순간으로 돌아갈 수 있음
- remote
	- Remote Repository - Github : github 등 외부에 위치한 원격 저장소
			- 협업에 용이


### 로컬 Git 저장소 생성
1. git init
	- 나의 프로젝트가 위치한 곳으로 이동 후 터미널에서 명령 실행
2. git ignore
	- 중요한 정보가 있는 파일들을 보안 유지를 위해 무시하도록 설정
3. git status
	- 어떤 파일들이 추적되고, 어떤 파일들이 Staging Area에 있는지 확인
4. git add
	- Staging Area 에 파일들을 저장 - 버전관리에 반영할 파일
	-  git add .
		현재 경로에 있는 모든 파일들을 add
	- git add "<파일명>"
		해당하는 파일명을 가진 파일을 add
	- git rm / reset
		원하지 않는 파일이 들어갔을 때 되돌리기
5. git commit -m "<메시지>"
	- Staging Area에 있는 파일들을 Local Repository로 commit
	- 로컬 저장소에 변동 기록을 남기고 commit을 통해 새로운 버전을 git에 등록
6. git branch -M main
	- main / master
		- 즉시 운영 배포할 수 있는 버전
		- 사용자가 지금 이용 중인 코드
	- staging
		- 상용에 반영하기 전 테스트 버전
	- feature
		- 새로운 기능 추가 개발 (병렬 작업)

### Github에 소스 반영
1.  git remote add origin <원격저장소 주소>
2. git push (-u origin main)

### Github로 협업하기
1. git clone <원격저장소 주소>
2. git checkout -b <브랜치명> / git checkout <브랜치명>
	- 기존 사용하던 브랜치에서 새로운 브랜치 생성 후 이동
3. git merge <브랜치명>
	- main 브랜치와 병합

### Git GUI 활용하기
- Sour cetree
- fork
- VS Code + GitLens