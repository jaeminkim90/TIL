> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# 주요 HTTP 메서드 5가지

HTTP는 다양한 메서드를 정의하고 있다. 자주 사용되는 주요 HTTP 메서드 5개를 살펴보자. 적절한 목적에 사용할 수 있도록 각각의 메서드의 특징과 주로 사용되는 상황을 정리하였다.

참고로 해당 메서드는 일종의 약속이다. 메서드 그 자체가 기능을 제공하는 것이 아니며, 메서드 이름에 알맞는 내용으로 서버에서 코드를 구현해야 한다.

- `GET`: 리소스 조회
(최근에는 Representation이라는 이름을 많이 사용한다.)
- `POST`: 요청 데이터 처리, 주로 등록에 사용 
- `PUT`: 리소스를 대체, 해당 리소스가 없으면 생성 
- `PATCH`: 리소스 부분 변경 
- `DELETE`: 리소스 삭제 

기타 메서드도 존재한다.
- `HEAD`: GET과 동일하지만 메시지 부분을 제외하고, 상태 줄과 헤더만 반환 
- `OPTIONS`: 대상 리소스에 대한 통신 가능 옵션(메서드)을 설명(주로 CORS에서 사용) 
- `CONNECT`: 대상 자원으로 식별되는 서버에 대한 터널을 설정 
- `TRACE`: 대상 리소스에 대한 경로를 따라 메시지 루프백 테스트를 수행 


---

## 1. GET 메서드 

```
GET /search?q=hello&hl=ko HTTP/1.1 
Host: www.google.com 
```
GET 메서드는 리소스의 조회를 위해 사용한다. 서버에 전달하고 싶은 데이터가 있다면 query(쿼리 파라미터, 쿼리 스트링)에 담아 보낸다. 서버는 타겟 리소스에 해당하는 representation data를 응답 데이터로 보낸다.

![](https://images.velog.io/images/woply/post/b95cadbe-757e-4c9d-b287-7fef984addda/image.png)
![](https://images.velog.io/images/woply/post/1ae3d175-c2fc-43f6-875f-8db56de7fde5/image.png)
![](https://images.velog.io/images/woply/post/e78d9d52-1d49-47ac-9ad5-587ad9dd67ce/image.png)

> **Q. GET 메서드로 요청할 때는 왜 query(쿼리 파라미터, 쿼리 스트링)를 써야 할까?**
GET 메서드도 POST 메서드와 같이 데이터를 담을 수 있는 메시지 바디가 존재한다. 하지만, GET 메서드의 메시지 바디를 확인하지 않는 서버가 대부분임으로 거의 사용되지 않는다. 
>
>GET은 리소스를 요청하기 위해 만들어 졌다. URL의 URI가 자원이 위치한 곳을 나타낸다. 메시지 바디에 데이터를 실어야 하는 상황은 거의 없다. 따라서, 일반적인 서버에서는 GET 요청의 메시지 바디를 확인하지 않는다. 실제로 메시지 바디에 데이터가 있더라도 처리되지 않을 확률이 높다.


---


## 2. POST 메서드
```
POST /members HTTP/1.1 
Content-Type: application/json

{
    "username": "hello",
    "age": 20 
}
```
POST 메서드는 요청 데이터의 처리를 목적으로 사용한다. 메시지 바디를 통해 서버로 요청 데이터를 전달하면, 서버는 정해진 로직에 따라 요청 데이터를 처리한다. POST 메서드를 사용한다고 특정한 로직이 자동으로 실행되는 것은 아니다. 리소스마다 POST 요청이 오면 어떻게 데이터를 처리할지 로직을 구현해야 한다. POST 매서드는 대상 리소스에 정의된 로직에 따라 고유한 기능을 수행한다. 주로, 전달된 데이터를 이용하여 신규 리소스를 등록하거나 프로세스를 처리한다. 

![](https://images.velog.io/images/woply/post/7e9d26c9-c3ac-4f22-8d0a-82de9de92c20/image.png)
![](https://images.velog.io/images/woply/post/ddb5d0c9-e6c5-41a0-b315-5d0d6310879b/image.png)
![](https://images.velog.io/images/woply/post/e335a4e7-17bc-4830-a662-13a23d1f9738/image.png)


**POST 메서드는 아래와 같은 작업을 요청할 때 사용된다.**    

1. HTML 양식으로 입력된 데이터 블록을 리소스 로직에 제공한다.
	- 예) HTML FORM에 입력한 정보로 회원 가입, 주문 등에서 사용
2. 게시판, 뉴스 그룹, 메일링 리스트, 블로그 또는 유사한 기사 그룹에 메시지 게시 
	-  예) 게시판 글쓰기, 댓글 달기 
3. 서버가 아직 식별하지 않은 새 리소스 생성 
	-  예) 신규 주문 생성 
4. 기존 자원에 데이터 추가
	-  예) 한 문서 끝에 내용 추가하기 
5. 단순히 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야 하는 경우 
	- 예) 주문에서 결제완료 -> 배달시작 -> 배달완료 처럼 단순히 값 변경을 넘어 프로세스의 상태가 변경되는 경우. POST의 결과로 새로운 리소스가 생성되지 않을 수도 있다. 
6. 기타
	-  애매한 경우 POST를 사용한다. 예) JSON으로 조회 데이터를 넘겨야 하는데, GET 메서드를 사용하기 어려운 경우. POST는 사실상 모든지 할 수 있다. 


---


## 3. PUT 메서드
```
PUT /members/100 HTTP/1.1 
Content-Type: application/json
{
	"username": "hello",
	"age": 20
}
```

PUT 메서드는 리소스 전체를 대체한다. 기존 리소스가 없을 경우 새로 생성한다. 즉, 덮어쓰기를 수행한다고 볼 수 있다. POST와 차이점은 클라이언트가 리소스의 위치를 알고 URI를 명시해야 한다는 점이다.

> **리소스가 이미 존재하는 경우, 기존 데이터를 대체한다.**
![](https://images.velog.io/images/woply/post/199c1006-975a-4a56-8786-dae5898f62d9/image.png) ![](https://images.velog.io/images/woply/post/cbff756f-e987-4a3e-aecc-5271addcdcfc/image.png)

> **리소스가 없는 경우, 새로운 데이터를 생성한다.**
![](https://images.velog.io/images/woply/post/476720be-9164-407a-814d-d5c9944e139d/image.png) ![](https://images.velog.io/images/woply/post/0801c8f6-b6e1-4f94-9489-cb52acb4154e/image.png)

> **주의할 점은 리소스를 완전히 대체한다는 것이다. 부분적인 수정이 불가능하다.**
![](https://images.velog.io/images/woply/post/5184b672-f62e-455a-b98e-6b8dfb9ecfd0/image.png) ![](https://images.velog.io/images/woply/post/9d6c94af-c6f8-4c6e-adb1-1b5d18c939ae/image.png)


---

## 4. PATCH 메서드
```
PATCH /members/100 HTTP/1.1 
Content-Type: application/json
{
	"age": 50
}
```

PATCH 메서드는 리소스를 부분 변경한다. 부분 변경이 필요한 상황에서 PATCH를 사용할 수 없다면 POST를 사용한다.

![](https://images.velog.io/images/woply/post/8480b33c-dd11-4594-80cf-b30757e0b97c/image.png)
![](https://images.velog.io/images/woply/post/3b9c6f66-9e24-4944-bd68-b19d95468517/image.png)


---


## 5. DELETE 메서드
```
DELETE /members/100 HTTP/1.1 
Host: localhost:8080
```
DELETE 메서드는 리소스를 제거한다.

![](https://images.velog.io/images/woply/post/2efb3d45-5231-45cc-909a-fad0554da63b/image.png)
![](https://images.velog.io/images/woply/post/f8f4874e-244b-4cf2-960d-3b9f620bf969/image.png)



