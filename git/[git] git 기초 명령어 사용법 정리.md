# Git 명령어 연습하기

> 이 포스팅은 유튜브 채널 '얄팍한 코딩사전'에 올라온 
'가장 쉬운 Git 강좌 - (상) 혼자작업편'을 보고 학습한 내용이다.
[링크: '가장 쉬운 Git 강좌 - (상) 혼자작업편'](https://youtu.be/FXDjmsiv8fI)
(*포스팅에 사용된 일부 이미지는 유튜브 영상에서 캡쳐하였다.)

---
## Git을 쉽게 설명한다면?
Git은 시간 여행이 가능한 평행 우주를 만드는 것과 같다. 시간 여행이 가능하다는 것은 언제든 과거의 상태로 되돌릴 수 있다는 뜻이다. 파일의 특정한 상태를 저장할 수 있고, 저장한 특정 시점으로 언제든 되돌아 갈 수 있다. 나아가 과거 작업 시점의 상태로 돌아가 필요한 내용만 챙겨서 현재나 다른 과거로 이동할 수도 있다.

Git은 매번 폴더에 특정 상태의 코드를 저장하는 수고를 대신해주기 때문에 매우 편리하다. Git은 폴더안에 여러 평행 우주를 생성해서 여기서는 이 버전, 저기서는 저 버전을 사용할 수 있게 도와준다.

평행우주처럼 자유롭게 여러 상태를 넘나들면서 작업을 이어갈 수 있다.

---
## **_Git을 사용하는 2가지 방법_**
git 사용법은 크게 2가지가 있다.

### 1) CLI(Commnad Line Interface)에서 명령어를 이용하는 방식
### 2) 소스트리와 같은 GUI 소프트웨어를 이용하는 방식

각각의 장점이 있으므로, 둘 다 사용하는 방법을 익히면 좋다.

---

## CLI와 소스트리로 Git 사용해보기
---

## 1. git 저장소 만들기

### **1) 작업 폴더를 만들고 연습해볼 파일을 작성한다.**
![](https://images.velog.io/images/woply/post/0585c169-339f-4f20-9a66-908f2bcca81b/image.png)
    

### **2) CLI에서 폴더의 경로를 찾아 들어간다**
![](https://images.velog.io/images/woply/post/d45db2ec-ac12-4651-8fc6-957af807d0bb/image.png)

3) 새로운 Git 저장소를 생성한다.
- CLI에 `git init`을 실행하면, 빈 저장소가 만들어 졌다는 메시지를 확인 할 수 있다.
![](https://images.velog.io/images/woply/post/e5d9f1c6-dd45-4f92-9bf2-19b832a4105a/image.png)
<br>


### **4) 이름과 이메일 등록하기**
- CLI에 아래와 같이 이름과 이메일을 등록할 수 있다.
`
git config user.name "Jaemin Kim"
`
`
git config user.email "jaeminkim90@gmail.com"
`
![](https://images.velog.io/images/woply/post/7ef1f7dc-619a-47e6-b9e7-a9c0a82ee965/image.png)

- **finder**에서 숨김 파일로 **.git**이라는 폴더가 생성된 것을 볼 수있다.
- **.git** 폴더 안에 모든 버전이 저장된다.
![](https://images.velog.io/images/woply/post/d6b9389b-5751-495d-8b89-a8388338d621/image.png)


### **5) 소스트리를 이용하여 동일한 과정을 진행할 수 있다.**
   
- CLI로 만들었던 .git 폴더를 삭제하고 소스트리를 이용하여 로컬 저장소를 만든다.
- 소스트리를 열고, 원격 저장소 생성하기를 클릭한다.
![](https://images.velog.io/images/woply/post/8efdb3bb-70b3-4f31-a738-f82ad792a4cd/image.png)
   
- 새로운 로컬 저장소 경로를 지정하고 생성한다.
- CLI로 만들었을 떄와 동일하게 .git  숨김 폴더가 만들어 진 것을 확인 할 수 있다.
![](https://images.velog.io/images/woply/post/04923a12-338a-44bf-8936-d8bf766ec148/image.png)

---
## 2. Git을 이용하여 현재 상태 저장하기
>가장 첫 번재로 앞서 만든 cat, mouse 파일을 현재 시점의 상태 그대로 git에 저장해보자.
현재 시점을 저장하면 미래의 시점에서 언제든지 저장된 시점의 파일로 돌아올 수 있다.

### 1) git에 저장할 파일 상태를 알려주는 'git status'
![](https://images.velog.io/images/woply/post/f96f06d2-bc03-4ebc-99a2-f7ad9256fa16/image.png)

- 아직 관리 상태에 포함되어 있지 않은 파일들을 확인 할 수 있다.
- cat과 mouse 파일이 untracked 상태임을 알려준다.
- untracked는 아직 git에서 버전을 관리(추적)하지 않고 있다는 것을 의미한다.
- 이 때 표기되는 cat과 mouse 파일 항목은 정확히는 실제 파일이 아니라, 이 파일들이 만들어졌다는 사실을 의미한다.
<br>

### 2) git의 관리(추적)에 파일을 포함하는 'git add -A'

![](https://images.velog.io/images/woply/post/5b1867f3-1e65-4ae8-9c46-13cf726f4d1e/image.png)
- 해당 경로의 모든 파일을 git의 타임캡슐에 넣는다는 의미가 된다.

![](https://images.velog.io/images/woply/post/f3103099-b775-4d12-bbd2-09691c1227c7/image.png)
- `git add -A` 실행 후 다시 `git status`를 실행하면 **'Changes to be committed'**에 **cat**과 **mouse** 파일이 표기된다. 
이 상태가 바로 **'git에 특정 시점의 상태를 저장(commit)'** 할 수 있는 상태를 의미한다. 
**git**에서는 이렇게 로컬 저장소에 특정 상태를 저장하는 것을 **커밋(commit)**이라고 지칭한다.
<br>

### 3) 특정 상태를 메모와 함께 git에 저장하는 'git commit -m (커밋 메시지 작성)'
![](https://images.velog.io/images/woply/post/a84fe03b-4e4a-4e99-8ff5-9912edd90975/image.png)
- **master**라는 이름을 가진 root에 2개의 새로운 파일이 commit 메시지 함께 추가되었다.

![](https://images.velog.io/images/woply/post/231e4e3b-844b-410e-8ad1-b693b0a82747/image.png)
- 다시 `git status`를 실행해 보면 새로 __commit__ 해야 할 파일이 없다고 나온다.
- 타임 캡슐에 무언가를 저장하려면 일단 캡슐안에 잘 담아야 하는 것처럼, 특정 상태의 파일들을 묶어 git에 저장할 준비(= 캡슐에 잘 담는 것) 단계가 바로 stage다. stage는 git에 저장되기 전 저장할 파일의 상태들이 선별되어 대기하고 있다고 볼 수 있다. CLI에서는 `add` 명령어를 통해 파일의 상태를 stage에 올린다.

![](https://images.velog.io/images/woply/post/bdb86550-0dbb-4afe-8463-c56d8c9258eb/image.png)

- stage에 위치했던 파일의 상태를 Git에 저장하는 것을 __commit__이라고 한다.
- __'파일 내용 수정 -> add -> stage -> commit'__의 과정을 거쳐 하나의 Git 버전으로 저장되는 것이다.

![](https://images.velog.io/images/woply/post/84f08495-122c-4a03-abbc-b6d331348810/image.png)
- __dog__ 파일을 새로 추가하면, 소스트리에서는 바로 __dog__ 파일을 stage에 올라가지 않은 파일로 표시한다.
- 클릭하면 간단한게 stage에 올릴 수 있고, commit 메시지를 작성하고 commit을 클릭하면 commit이 완료된다.

![](https://images.velog.io/images/woply/post/3092a41a-48b0-4322-8e80-cd8755f0cfdf/image.png)

- CLI에서는  'git status' 명령어를 실행하면 stage에 올라가지 않은 상태의  dog 파일을 표시해준다.
- `git add` 명령어를 이용하여 stage에 올릴 수 있고, `git commit` 명령어를 통해 commit 메시지와 함께 commit 할 수 있다.


![](https://images.velog.io/images/woply/post/b31b6316-bbfc-4657-93df-b0bc1ad6cdb0/image.png)

- 파일의 상태를 아래와 같이 변경 후에 `git status` 명령어를 통해 변경된 상태를 확인할 수 있다.
1) cat 파일을 삭제했다.
2) mouse 파일은 name을 mickey로 변경했다.
3) penguin 파일을 새로 만들고 내용에 'name: pororo'를 추가했다.
- penguin 폴더가 '생성' 되었다는 사실 뿐만 아니라 mouse 파일이 '변경' 되었다는 사실과 cat 파일이 '삭제' 되었다는 사실이 함께 표시된다.

![](https://images.velog.io/images/woply/post/ad784623-0116-40f9-9224-392cf6ac2a50/image.png)
- 소스트리에서도 같은 내용을 자동으로 업데이트하여 보여준다. 

![](https://images.velog.io/images/woply/post/058d1a2a-b5b2-42d2-bce2-feca76fe9345/image.png)

- commit하기 위해 add하여 stage에 먼저 올려주고, 메시지와 함께 commit을 실행한다.
- commit이 완료 되면 git은 현재까지의 모든 변화 상태를 저장한다.
<br>

### 4) 지금까지의 commit 작업을 한 번에 확인 할 수 있는 'git log'

![](https://images.velog.io/images/woply/post/04057635-7356-4520-aa30-5ea8e0d02a49/image.png)
![](https://images.velog.io/images/woply/post/d70ff446-206c-42a3-87fb-87cde015f580/image.png)

- 소스트리에서는 히스토리 탭에서 쉽게 작업 내역을 확인할 수 있다.

---
## 3. Git을 이용하여 과거 상태로 되돌아가기
>과거 상태로 되돌아 가는 방법은 2가지가 있다.
**1) 과감한 방법 - `reset`**
**2) 신중한 방법 - `rebase`**


### 1) 과감한 방법 'git reset'
![](https://images.velog.io/images/woply/post/e0c2a8fc-04cd-46c1-9160-8d70d48378bd/image.png)
- 과감한 방법은 과거의 특정 시점으로 돌아 가되, 그 시점 이후에 발생했던 모든 변경 사항을 삭제하는 방식이다.
- 과거의 시점이 현재가 되고 그 사이의 기록은 사라진다고 이해 할 수 있다.

![](https://images.velog.io/images/woply/post/84233f34-c67f-4d96-aca7-8c9549747416/image.png)
- `git log`를 통해 변경 내용을 조회하고, 돌아가길 원하는 시점의 commit 해시값 중 앞 6자리를 복사한다.
- 6자리를 이용해 아래와 같이 `git reset (6자리 해시값) --hard` 명령어를 실행한다.

![](https://images.velog.io/images/woply/post/4482d09c-8691-4b2e-8bde-a3682f38abc9/image.png)
- 현재 6자리에 해당하는 상태 버전으로 시점이 이동했다는 메시지가 표시된다.

![](https://images.velog.io/images/woply/post/ac4c1ed8-6d8c-4789-866a-ffab31119480/image.png)
- VScode를 통해 실제 작업 했던 파일들을 살펴보면 생성했던 penguin 파일은 사라져 있고, 삭제 했던 cat 파일은 다시 나타났고, 내용을 mickey로 수정 했던 mouse 파일은 수정 이전 상태인 jerry로 변경되어 있다.

![](https://images.velog.io/images/woply/post/31119a16-73cd-4331-8d4a-6820f017f6ed/image.png)
- 동일한 작업을 소스트리에서도 실행하기 위해 변경사항을 다시 적용했다.
- 소스트리에서 원하는 시점의 버전에서 __'master를 이 커멋으로 초기화'__ 한다.

![](https://images.velog.io/images/woply/post/6831423a-535e-4ec4-ade6-23513266edd0/image.png)
- 옵션에서 hard를 선택한다.

![](https://images.velog.io/images/woply/post/ceb16359-fe4f-4fd0-a46e-c63c196b5017/image.png)
- 파괴적인 작업이라는 경고창이 나타난다. 확인을 클릭한다.

![](https://images.velog.io/images/woply/post/447953a0-6571-4f6c-bc65-49ba2b2ce84a/image.png)
- CLI에서 마찬가지로 'reset'을 했던 것과 마찬가지로, penguin 파일은 사라져 있고, 삭제 했던 cat 파일은 다시 나타났고, 내용을 mickey로 수정 했던 mouse 파일은 수정 이전 상태인 jerry로 변경되어 있다.

![](https://images.velog.io/images/woply/post/8126550b-3d29-4655-b06d-66c51d69930f/image.png)
- 소스트리 상에서도 되돌아간 시점 이후의 상태가 모두 사라져 있다.
<br>

### 2) 신중한 방법 'git rebase'
- 돌아갈 시점이 아닌 취소할 시점을 찾는 방식이다.
- 미래에 한 발을 걸치고 과거로 가는 방법에 해당한다.

![](https://images.velog.io/images/woply/post/400f0387-e1be-4d29-bec2-f6827355d070/image.png)
- 다시 한 번 penguin 파일 생성, cat 파일 삭제, mouse 파일 내용을 수정하고 commit한다.
- 이번에는 돌아갈 시점이 아닌 __'취소할 commit 시점'__을 찾아 해시 값 앞 6자리와 함께 아래 `git revert (6자리 해시값)`명령어를 실행한다.

![](https://images.velog.io/images/woply/post/609171f3-498e-46c1-abaf-c1e110a26bbb/image.png)
- 새 commit 메시지를 작성하는 화면이 나타난다.
- 그대로 저장하겠다는 명령어 `:wq`을 입력하면 `reset` 했을 때와 마찬가지로 파일들이 과거 상태로 돌아와 있다.
-`reset`이 과거로 되돌아가는 개념이라면 `revert`는 상태 저장을 취소한다는 변경사항을 또 하나의 상태로 commit하는 개념이다.

![](https://images.velog.io/images/woply/post/d4555dc9-acf0-4ee2-84d1-6c0f069bffea/image.png)
`git log`를 통해 상태 변화를 살펴보면, reset처럼 특정 시점으로 돌아오면서 그 이후에 발생했던 commit은 모두 삭제되는 방식이 아니다.
- `revert`를 하면 __commit를 원복한 상태__를 새로운 상태로 저장한다.
![](https://images.velog.io/images/woply/post/68f059f1-7390-468b-a989-b5b5e40cb71f/image.png)
- 소스트리에서 '히스토리'를 살펴보면 더 직관적으로 차이를 이해할 수 있다.

![](https://images.velog.io/images/woply/post/b84dda5d-f934-4d6d-9149-0a60f22eb8d2/image.png)
![](https://images.velog.io/images/woply/post/781127bb-2d46-4641-a219-d372ff47b03c/image.png)
- 마지막 commit에 해당하는 revert의 변경 사항을 살펴보자. 삭제되었던 cat 파일이 추가되고, 내용이 수정되었던 mouse 파일이 예전 내용으로 수정되고, 추가로 생성되었던 penguin 파일이 삭제된 것을 확인 할 수 있다.
- 마찬가지로 소스트리에서 특정 시점으로 초기화 할 수 있고, revert를 통해 해당 commit의 적용 사항을 반대로 적용하여 새로운 commit을 만들 수 있다.

![](https://images.velog.io/images/woply/post/4598729b-82c6-400e-b14a-690b4f8d167c/image.png)
![](https://images.velog.io/images/woply/post/b7152e81-ca25-4f45-a61c-061eb6b6cbe3/image.png)
![](https://images.velog.io/images/woply/post/09b1c432-6cd8-4ec9-88df-a5e5d816d4cb/image.png)
- 마찬가지로 소스트리에서 특정 시점으로 초기화 할 수 있고, revert를 통해 해당 commit의 적용 사항을 반대로 적용하여 새로운 commit을 만들 수 있다.


---

## 4. branch를 이용하여 여러개의 상태 이동하기
>branch는 특정 시점의 상태애서 병렬로 진행되는 또 다른 상태를 만드는 작업이다. branch가 생성된 직후에 상태 파일은 2개 이지만, 정확하게 동일한 상태를 가진다. branch가 되면 특정 시점에서 나눠져 병렬 형태로 각기 다른 상태를 만들어 갈 수 있다. 복제된 상태가 새로 만들어진 것과 같다.  

![](https://images.velog.io/images/woply/post/0372dc1b-88bf-4452-8942-7a9743743f28/image.png)
- branch를 생성하려면 `git branch` 명령어를 이용한다.

![](https://images.velog.io/images/woply/post/29b77540-86c4-4bb5-9ef7-63d46db60bf8/image.png)
- branch를 생성하고 'git branch'를 실행하면 추가로 생성된 branch를 확인 할 수 있다.
- *표시는 현재 초점이 맞춰져있는(작업 대상에 해당하는) branch이다. 

![](https://images.velog.io/images/woply/post/33d0af98-9834-4773-96b4-f1f63f05f25d/image.png)
- 작업 branch를 변경하려면 `git checkout` 명령어를 이용한다.
- branch가 변경되었음을 알 수 있다. 이 때는 원래 branch의 상태와 동일하기 때문에 상태에 변화가 없다.

![](https://images.velog.io/images/woply/post/d2ab776e-1182-45d8-bd65-3f256ce9260b/image.png)
- git을 통해 서로 다른 branch의 변경된 내용을 반영하고, 합치는  작업을 해보기 위해 간단한 수정 작업을 진행한다.
- my-idea branch에서 mouse 파일을 삭제하고, dinosaur 파일을 생성한 후 내용에 "name: dooly"를 추가한다. 
commit을 실행하면, 기존의 master branch와 my-idea branch는 서로 다른 상태를 갖게 된다.

![](https://images.velog.io/images/woply/post/57c3a2f7-9d52-4b4b-80c8-e86bb6c17c0b/image.png)
- 이때 master branch로 돌아가려면 `git checkout (브랜치의 이름)`을 실행한다.
- branch가 master로 변경된다.

![](https://images.velog.io/images/woply/post/ab3e32aa-77d0-4a93-aa0f-edd5b9e8e515/image.png)
![](https://images.velog.io/images/woply/post/f932397c-b535-45af-b04b-1504c091e96e/image.png)
- 소스트리에서는 우측 브랜치 메뉴에서 간단하게 원하는 branch 이름을 더블 클릭하면 branch가 변경된다.
- branch가 변경되면 코드의 상태도 변경된다.

![](https://images.velog.io/images/woply/post/8e5521d6-4212-43f0-9ce6-1e5b69736f65/image.png)
- 소스트리에서는 우측 상단의 브랜치 아이콘을 클릭하여, 새로운 branch를 생성할 수 있다.
- my-idea에서 파생된 branch이기 때문에 생성된 시점에서는 my-idea branch와 my-another-idea branch의 상태가 정확하게 동일하다.

![](https://images.velog.io/images/woply/post/f0979472-2ca7-4119-89eb-729321daee4a/image.png)
- 다시 my-another-idea branch에서 cat 파일의 내용을 'name: nyang'으로 변경하고, commit을 한다.
- 서로 다른 branch 3개를 이동하면서 필요한 내용을 master 브런치로 가져와 적용하는 연습을 해보자. 
상태를 구분하기 위해 master braanch의 dog 파일 내용을 'name: mong'으로 수정하고 commit 한다.

- 현재 branch 각각의 상태는 아래와 같다.
master: cat-tom / dog-mong / mouse-jerry
my-idea: cat-tom / dog-snoopy / dinosaur-dooly
my-another-idea: cat-nyang / dog-snoopy / dinosaur-dooly

---

## 5-1. merge를 이용해 다른 branch에서 상태를 가져오기 
>현재 서로 다른 상태를 가진 3개의 branch를 하나의 branch로 합치는 작업을 merge라고 한다.
my-another-idea branch는 my-idea branch에서 파생되어 만들어진 branch다.
my-idea branch의 내용을 포함하고 있기 때문에 my-another-idea branch를 master bracnch로 합치면 세 개의 branch 내용이 모두 합쳐진다.

![](https://images.velog.io/images/woply/post/e2ad8b32-d909-48e3-91e5-87562d6467a1/image.png)
- 먼저, 'checkout' 명령어를 이용하여  master branch에 초점을 둔다.
- `git merge (브랜치의 이름)`을 실행하여 master branch에 합칠 branch를 가져온다. 
- commit message를 작성하고 `:wq` 명령어를 실행한다.

![](https://images.velog.io/images/woply/post/ce354d28-8e34-4c78-a6ab-c75cd8264510/image.png)
- merge 이전의 상태는 아래와 같다.
master: cat-tom, dog-mong, mouse-jerry
my-idea: cat-tom, dog-snoopy, dinosaur-dooly
my-another-idea: cat-nyang, dog-snoopy, dinosaur-dooly
- merge 이후 master의 상태는 아래와 같다.
master: cat-nyang, dog-mong, dinosaur-dooly
- my-another-idea의 상태였던 cat-nyang과 dinosaur-dooly가 반영되었고, mouse는 삭제 되었다.

![](https://images.velog.io/images/woply/post/1bba26d4-22fa-42ad-ba66-f67cfd294a04/image.png)
- 소스트리의 브랜치 그래프를 보면 branch의 분기와 merge 과정을 더 직관적으로 확인할 수 있다.
- 하단의 변경 내용은 merge되면서 어떤 내용이 추가,수정,삭제 되었는지 쉽게 확인할 수 있다.

![](https://images.velog.io/images/woply/post/8b1c8da8-0272-4240-8e37-f0faade8df43/image.png)
- 소스트리의 '브랜치 그래프'는 CLI에서 `git log --graph --all --decorate` 명령어를 통해 확인 할 수 있다.

![](https://images.velog.io/images/woply/post/aabe6ae4-ac59-47a0-b41f-b03c3d3bbc70/image.png)
- 소스트리를 이용한 merge 작업은 더 간단하다.
- 먼저, 소스트리에서 동일한 merge 작업을 수행하기 위해 merge 전 시점으로 돌아간다
- 현재 master의 상태는 dog-mong, mouse-jerry, cat-tom이다.

![](https://images.velog.io/images/woply/post/e6c93ac9-89d6-4932-9db7-95117b9b54bb/image.png)
![](https://images.velog.io/images/woply/post/201e73a9-660e-47d2-8945-e70d9b9360b8/image.png)
![](https://images.velog.io/images/woply/post/f900ec60-9c05-44b1-bf7a-1cb49e10fecf/image.png)
- 소스트리에서는 병합할 branch를 우클릭하고 merge해주면 간단하게 merge된다.


## 5-2 merge 중 상태가 변경된 내용이 충될하는 경우 
>동일한 파일의 동일한 코드라인에서 상태가 변경되었다. 이후 두 branch가 merge될 때 충돌이 발생한다. 내용을 정리해 준 후에 다시 commit을 시도하면 수정된 내용으로 merge된다.

예시 상황을 통해 충돌하는 경우를 확인해 보자.
![](https://images.velog.io/images/woply/post/389a1b40-1862-462b-9e04-e47ff70bd199/image.png)
- bark-wang branch를 새로 만들고, bark-wang으로 초점을  변경한다.

![](https://images.velog.io/images/woply/post/1c89762c-8b49-4f84-b980-cc8c6957b0e4/image.png)
- dog 파일에 bark: wang wang을 추가하고 commit 한다.
- 다시 master branch로 돌아와서 이번엔 `bark: warl warl`을 입력하고 commit한다

![](https://images.velog.io/images/woply/post/d1bf7c5c-0d59-4707-8a4e-fb0ac8e47925/image.png)
![](https://images.velog.io/images/woply/post/02f450d4-fc40-4084-8610-d71f9534f7b9/image.png)
- 현재 master branch와 bark-wang branch에서 dog 파일이 서로 다른 내용으로 변경되었다. 같은 파일의 같은 부분에 수정이 발생했다.
- branch 마다 수정 된 내용이 다르면 merge가 매끄럽게 진행된다. 하지만 같은 파일 내 같은 라인에 수정이 발생한 두 branch를 merge 할 경우 __CONFLICT__가 발생한다.

![](https://images.velog.io/images/woply/post/eb72fd42-cfcc-4364-a0d0-0de5a434b6ea/image.png)
![](https://images.velog.io/images/woply/post/9b4c8093-1c1a-4748-a7d4-f74e2a0028fb/image.png)
- 중복된 내용을 표시해 준다. 한 쪽 내용을 지워주고 다시 commit 한다.

![](https://images.velog.io/images/woply/post/19bc8b59-27ef-4446-9888-fbe9075a51bc/image.png)
- 정상적으로 merge되었다. 

![](https://images.velog.io/images/woply/post/bb1e3f29-22c1-47fd-93ca-2cf5cbafa954/image.png)
![](https://images.velog.io/images/woply/post/b0d9bc7d-1aa5-4c33-88bb-ae087ff06fab/image.png)

-`git log --graph --all --decorate` 명령어를 이용해 merge 그래프를 확인해 보면 정상적으로 완료된 merge 그래프를 볼 수 있다.

![](https://images.velog.io/images/woply/post/faf9ab55-1c97-4318-828a-fb1efe5c3ce0/image.png)
- 같은 내용을 소스트리에서도 확인 할 수 있다.

> ## 5-3 다른 branch의 변경 과정(commit line)을 하나의 branch에 하나의 변경 과정(commit line)으로 합치기
branch를 분리했다가 다시 합치는 방식이 아니라, 서로 다른 __상태 변화 과정__을 가진 branch들을 마치 하나의 branch인듯 모든 상태 과정을 합치는 것이 `rebase`다.
![](https://images.velog.io/images/woply/post/909132ee-1a72-41fb-b2e2-7b9933998ef8/image.png)
- merge와 rebase의 차이 비교

![](https://images.velog.io/images/woply/post/1c413bb4-1f96-4d2c-bdf1-08058cbce354/image.png)
- 여러개의 상태 변경을 가진 branch와 master branch의 상태 변경 과정을 합칠 때는 `git rebase (브랜치의 이름)` 명령어를 실행한다.


![](https://images.velog.io/images/woply/post/a4cc481c-42a8-45fd-bbb5-64d6737898af/image.png)
- branch를 삭제할 땐 `git branch -D (브랜치의 이름)` 명령어를 이용한다.

![](https://images.velog.io/images/woply/post/3d822aaf-ee36-4051-89b3-9c5feb89911a/image.png)
- 소스트리에서는 우클릭을 통해 더 간편하게 삭제 할 수 있다.

---

## 📌 Git 명령어 정리

**Git 저장소 생성**
`git init`

**내 정보들 입력**
`git config -g user.name “(내 이름)” `
`git config -g user.email “(내 메일주소)”`

**폴더 내 변화들과 캡슐 상태 확인**
`git status`

**폴더 내 변화 캡슐에 담기 (스테이지)**
`git add -A`

**변화를 담은 캡슐 묻기 (현 상태를 저장)**
`git commit -m “(적어둘 내용)”`

**git 상태들 확인(일련번호 등)**
`git log`

**특정 시점으로 돌아가기 (❗️복구불가능)**
`git reset (돌아갈 커밋의 일련번호 앞 6자리) --hard`

**특정 상태로 돌아가기 (변화를 상쇄)**
`git revert (상쇄할 커밋의 앞 6자리)`

**새 브랜치 만들기**
`git branch (새 브랜치명)`

**만들어진 브랜치들 보기**
`git branch`

**특정 브랜치로 이동**
`git checkout (브랜치명)`

**💡 브랜치 만들고 바로 그리로 이동**
`git checkout -b (브랜치명)`

**다른 브랜치의 변경사항 가져오기**
`git merge (대상 브랜치명)`

**다른 브랜치의 변경사항 가져오기(이력 깔끔히)**
`git rebase (대상 브랜치명)`

**브랜치 삭제
`git branch -d (삭제할 브랜치명)`
(*d는 대소문자 무관)