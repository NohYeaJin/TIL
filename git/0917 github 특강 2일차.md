# github pages

- 깃헙이 제공해주는 웹 호스팅 서비스 

- 깃헙이 조금 떼서 웹을 만들 수 있는 서비스 제공

- 각각의 레포지토리에 대해서 각각의 깃헙 페이지들을 둘 수 있음

- https://nohyeajin.github.io/
  => NohYeaJin.github.io 로 들어가면 페이지가 생겨난다 

- github repo마다 pages가 있음 (설정 들어가면)

- 어떤 브랜치의 어디 폴더에 있는 index.html을 읽어올건지 지정하면 

  => 그 페이지가 뜬다 

  git pull origin master

  여기에서의 master는 내 로컬에서의 master가 있다

  => push전에는 pull이 있다 

# pull

git clone url

git pull origin master

=>이렇게 하면 pull



# conflict

- 먼저 pull하고 push해라 

  => 헷갈려하는 git 

  => 잘 가져왔지만 그래서 7번째줄에 어떤 데이터를 둬야해?

  => git은 모든 데이터를 다 가져오긴 함 => 이 파일이 가져야할 최종 상태를 만들어주면 된다 

  => current change (지금 변경된 것), incoming change(변경 받아온 것)

  => 이리저리 조정하고 push하면 된다

# add를 취소

- git status를 하면 변경, 삭제, 삽입된 파일들 다 나옴
- 그 중에서 파일을 골라서 
- git restore --staged 파일이름
- => 하면 취소 된다 

# working directory 변경사항 취소

- 조심해야 한다 !!!!
- 어디까지 내 작업이 날라가는지 알아야 한다
- 가장 최신 커밋의 상태로 돌려버리는 것 
- git resotre {file}

# commit 되돌리기

- git reset => 자세하게 다루기에 시간 부족
- git rebase => 이것도 
- hard option말고도 soft , mixed가 있음 
- git reset은 커밋을 되돌리는 역할을 하는 구나 정도로 알기 
- hard option은 wd, sa, rp를 모두 해당 커밋의 상태로 돌려놓는다
- mixed 는 wd를 제외하고 sa, rp만 돌려놓는다
- soft는 rp만 되돌린다 

# gitignore 원하지 않는 파일 제외하기

- git한테 어떤 파일은 무시해라 라고 말하는 것
- .git이 있는 위치에 있어야 한다
- .gitignore파일 만들기
- data.csv => 파일 명시
- *.png => 파일형식 명시 
- !profile.png => profile.png파일 제외한 모든 png파일 무시
- 파일명이 일치만 하면 폴더 트리 구조 막론하고 그냥 비활성해준다
- secret/ => 특정 폴더 자체 제외 
- 아무것도 없더라고 .gitignore는 미리 만들어두는 것이 좋다 

# 레포 만들 때 무조건 만들기

- 리드미
- .gitignore

# gitignore.io

- 운영체제, IDE, 언어별로 알아서 git ignore파일을 만들어준다 
- #은 주석이니까 설명 

# shared Repository Model

- 저장소 공유 모델 
- collaborator도 push 할 수 있다
- settings => manage access => invite collaborator

# branch

- 브랜치 = 특정 커밋을 가리키는 하나의 포인터 

- 브랜치를 나눴다가 합치는 것을 merge라고 한다 

- 브랜치 생성 : git branch 이름

  (특정 위치 master 위치에 이걸 하면 두개의 branch가 가리키는 것 )

- 이동: git checkout  이름

- 생성 및 이동: git checkout -b 이름

- 목록: git branch (커밋이 없으면 목록에 뜨지 않는다)

- 삭제: git branch -d 이름 

# merge

merge할 때 서로 다른 commit에서 동일한 파일 수정하면 충돌이 난다 => 직접 수정 필요

# 그래프

- git log --graph
- git log --graph --oneline

fast foward merge(무임승차 느낌) 와 

merge commit을 만들어서 합치는 merge가 있다 

# branch merge 

# pull request

# git flow

git을 활용하여 협업하는 흐름 

branch 사용 전략 

git flow, git lab flow 등

- master :배포 가능한 상태의 코드

- develop(main): feature branch 로 나뉘어지거나, 발생한 버그 수정 등 개발 진행,

  개발 이후 release branch로 갈라짐(모든 개발은 여기서 시작한다)

- feature branches (supporting): 기능별 개발 브랜치 (topic branch)

  기능이 반영되거나 드랍되는 경우 브랜치 삭제

- release branches(supporting): 개발 완료 이후 QA/Test 등을 통해 얻어진 다음

  배포 전 minor bug fix 등 반영

- hotfixes(supporting): 긴급하게 반영해야하는 bug fix

  release branch는 다음 버전을 위한 것이라면, hotfix branch는 현재 버전을 위한 것 

# git hub flow models

- shared repository model (코드 리뷰 후에 push가능 )
- fork & pull model  (내가 push할수있는 권한이 없음) => 오픈소스에서 사용 
- 가장 큰 차이점은 내가 해당프로젝트 저장소에 직접적인 push 권한이 있는지 여부
- 오픈 소스: 다 공개한 코드들 (위키백과 같은 공동의 지성을 믿는 것 )=> 점점 발전해나가는 구조
- 딥러닝에서 1세대로 유명했던 것이 tensorflow(딥러닝 가능한 파이썬 라이브러리)

# open source 참여하기

- A remote repo를 나의 remote repo로 가져온다 (이걸 fork라고 한다)

- remote repo를 local에 클론한다 

- 로컬에 새로운 브랜치 만들어서 작업 

- 작업 끝나면 push 해야한다 => 그러나 권한이 없음 

- 내가 fork한 remote repo에 push 한다 

- 이제 내가 개발한걸 합쳐달라고 PR을 보낸다 

- 관리자가 보고 넣어주면 collaborator가 된다 

  ----

- 오른쪽 위에 fork버튼을 누른다 

- 가져와서 이리저리 수정한다

- pull request를 보낸다 base repository로 



# github flow

1. master 브랜치는 배포가능한 상태여야
2. feature branch는 각 기능의 의도를 알 수있도록 작성
3. commit message는 매우 중요, 명확하게 작성
4. pull request를 통해 협업 진행
5. 변경사항 반영하고 싶다면 master branch에 병합한다 







