> ### π βοΈ 
> 1. **TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---

# μ£Όμ HTTP λ©μλ 5κ°μ§

HTTPλ λ€μν λ©μλλ₯Ό μ μνκ³  μλ€. μμ£Ό μ¬μ©λλ μ£Όμ HTTP λ©μλ 5κ°λ₯Ό μ΄ν΄λ³΄μ. μ μ ν λͺ©μ μ μ¬μ©ν  μ μλλ‘ κ°κ°μ λ©μλμ νΉμ§κ³Ό μ£Όλ‘ μ¬μ©λλ μν©μ μ λ¦¬νμλ€.

μ°Έκ³ λ‘ ν΄λΉ λ©μλλ μΌμ’μ μ½μμ΄λ€. λ©μλ κ·Έ μμ²΄κ° κΈ°λ₯μ μ κ³΅νλ κ²μ΄ μλλ©°, λ©μλ μ΄λ¦μ μλ§λ λ΄μ©μΌλ‘ μλ²μμ μ½λλ₯Ό κ΅¬νν΄μΌ νλ€.

- `GET`: λ¦¬μμ€ μ‘°ν
(μ΅κ·Όμλ Representationμ΄λΌλ μ΄λ¦μ λ§μ΄ μ¬μ©νλ€.)
- `POST`: μμ²­ λ°μ΄ν° μ²λ¦¬, μ£Όλ‘ λ±λ‘μ μ¬μ© 
- `PUT`: λ¦¬μμ€λ₯Ό λμ²΄, ν΄λΉ λ¦¬μμ€κ° μμΌλ©΄ μμ± 
- `PATCH`: λ¦¬μμ€ λΆλΆ λ³κ²½ 
- `DELETE`: λ¦¬μμ€ μ­μ  

κΈ°ν λ©μλλ μ‘΄μ¬νλ€.
- `HEAD`: GETκ³Ό λμΌνμ§λ§ λ©μμ§ λΆλΆμ μ μΈνκ³ , μν μ€κ³Ό ν€λλ§ λ°ν 
- `OPTIONS`: λμ λ¦¬μμ€μ λν ν΅μ  κ°λ₯ μ΅μ(λ©μλ)μ μ€λͺ(μ£Όλ‘ CORSμμ μ¬μ©) 
- `CONNECT`: λμ μμμΌλ‘ μλ³λλ μλ²μ λν ν°λμ μ€μ  
- `TRACE`: λμ λ¦¬μμ€μ λν κ²½λ‘λ₯Ό λ°λΌ λ©μμ§ λ£¨νλ°± νμ€νΈλ₯Ό μν 


---

## 1. GET λ©μλ 

```
GET /search?q=hello&hl=ko HTTP/1.1 
Host: www.google.com 
```
GET λ©μλλ λ¦¬μμ€μ μ‘°νλ₯Ό μν΄ μ¬μ©νλ€. μλ²μ μ λ¬νκ³  μΆμ λ°μ΄ν°κ° μλ€λ©΄ query(μΏΌλ¦¬ νλΌλ―Έν°, μΏΌλ¦¬ μ€νΈλ§)μ λ΄μ λ³΄λΈλ€. μλ²λ νκ² λ¦¬μμ€μ ν΄λΉνλ representation dataλ₯Ό μλ΅ λ°μ΄ν°λ‘ λ³΄λΈλ€.

![](https://images.velog.io/images/woply/post/b95cadbe-757e-4c9d-b287-7fef984addda/image.png)
![](https://images.velog.io/images/woply/post/1ae3d175-c2fc-43f6-875f-8db56de7fde5/image.png)
![](https://images.velog.io/images/woply/post/e78d9d52-1d49-47ac-9ad5-587ad9dd67ce/image.png)

> **Q. GET λ©μλλ‘ μμ²­ν  λλ μ query(μΏΌλ¦¬ νλΌλ―Έν°, μΏΌλ¦¬ μ€νΈλ§)λ₯Ό μ¨μΌ ν κΉ?**
GET λ©μλλ POST λ©μλμ κ°μ΄ λ°μ΄ν°λ₯Ό λ΄μ μ μλ λ©μμ§ λ°λκ° μ‘΄μ¬νλ€. νμ§λ§, GET λ©μλμ λ©μμ§ λ°λλ₯Ό νμΈνμ§ μλ μλ²κ° λλΆλΆμμΌλ‘ κ±°μ μ¬μ©λμ§ μλλ€. 
>
>GETμ λ¦¬μμ€λ₯Ό μμ²­νκΈ° μν΄ λ§λ€μ΄ μ‘λ€. URLμ URIκ° μμμ΄ μμΉν κ³³μ λνλΈλ€. λ©μμ§ λ°λμ λ°μ΄ν°λ₯Ό μ€μ΄μΌ νλ μν©μ κ±°μ μλ€. λ°λΌμ, μΌλ°μ μΈ μλ²μμλ GET μμ²­μ λ©μμ§ λ°λλ₯Ό νμΈνμ§ μλλ€. μ€μ λ‘ λ©μμ§ λ°λμ λ°μ΄ν°κ° μλλΌλ μ²λ¦¬λμ§ μμ νλ₯ μ΄ λλ€.


---


## 2. POST λ©μλ
```
POST /members HTTP/1.1 
Content-Type: application/json

{
    "username": "hello",
    "age": 20 
}
```
POST λ©μλλ μμ²­ λ°μ΄ν°μ μ²λ¦¬λ₯Ό λͺ©μ μΌλ‘ μ¬μ©νλ€. λ©μμ§ λ°λλ₯Ό ν΅ν΄ μλ²λ‘ μμ²­ λ°μ΄ν°λ₯Ό μ λ¬νλ©΄, μλ²λ μ ν΄μ§ λ‘μ§μ λ°λΌ μμ²­ λ°μ΄ν°λ₯Ό μ²λ¦¬νλ€. POST λ©μλλ₯Ό μ¬μ©νλ€κ³  νΉμ ν λ‘μ§μ΄ μλμΌλ‘ μ€νλλ κ²μ μλλ€. λ¦¬μμ€λ§λ€ POST μμ²­μ΄ μ€λ©΄ μ΄λ»κ² λ°μ΄ν°λ₯Ό μ²λ¦¬ν μ§ λ‘μ§μ κ΅¬νν΄μΌ νλ€. POST λ§€μλλ λμ λ¦¬μμ€μ μ μλ λ‘μ§μ λ°λΌ κ³ μ ν κΈ°λ₯μ μννλ€. μ£Όλ‘, μ λ¬λ λ°μ΄ν°λ₯Ό μ΄μ©νμ¬ μ κ· λ¦¬μμ€λ₯Ό λ±λ‘νκ±°λ νλ‘μΈμ€λ₯Ό μ²λ¦¬νλ€. 

![](https://images.velog.io/images/woply/post/7e9d26c9-c3ac-4f22-8d0a-82de9de92c20/image.png)
![](https://images.velog.io/images/woply/post/ddb5d0c9-e6c5-41a0-b315-5d0d6310879b/image.png)
![](https://images.velog.io/images/woply/post/e335a4e7-17bc-4830-a662-13a23d1f9738/image.png)


**POST λ©μλλ μλμ κ°μ μμμ μμ²­ν  λ μ¬μ©λλ€.**    

1. HTML μμμΌλ‘ μλ ₯λ λ°μ΄ν° λΈλ‘μ λ¦¬μμ€ λ‘μ§μ μ κ³΅νλ€.
	- μ) HTML FORMμ μλ ₯ν μ λ³΄λ‘ νμ κ°μ, μ£Όλ¬Έ λ±μμ μ¬μ©
2. κ²μν, λ΄μ€ κ·Έλ£Ή, λ©μΌλ§ λ¦¬μ€νΈ, λΈλ‘κ·Έ λλ μ μ¬ν κΈ°μ¬ κ·Έλ£Ήμ λ©μμ§ κ²μ 
	-  μ) κ²μν κΈμ°κΈ°, λκΈ λ¬κΈ° 
3. μλ²κ° μμ§ μλ³νμ§ μμ μ λ¦¬μμ€ μμ± 
	-  μ) μ κ· μ£Όλ¬Έ μμ± 
4. κΈ°μ‘΄ μμμ λ°μ΄ν° μΆκ°
	-  μ) ν λ¬Έμ λμ λ΄μ© μΆκ°νκΈ° 
5. λ¨μν λ°μ΄ν°λ₯Ό μμ±νκ±°λ, λ³κ²½νλ κ²μ λμ΄μ νλ‘μΈμ€λ₯Ό μ²λ¦¬ν΄μΌ νλ κ²½μ° 
	- μ) μ£Όλ¬Έμμ κ²°μ μλ£ -> λ°°λ¬μμ -> λ°°λ¬μλ£ μ²λΌ λ¨μν κ° λ³κ²½μ λμ΄ νλ‘μΈμ€μ μνκ° λ³κ²½λλ κ²½μ°. POSTμ κ²°κ³Όλ‘ μλ‘μ΄ λ¦¬μμ€κ° μμ±λμ§ μμ μλ μλ€. 
6. κΈ°ν
	-  μ λ§€ν κ²½μ° POSTλ₯Ό μ¬μ©νλ€. μ) JSONμΌλ‘ μ‘°ν λ°μ΄ν°λ₯Ό λκ²¨μΌ νλλ°, GET λ©μλλ₯Ό μ¬μ©νκΈ° μ΄λ €μ΄ κ²½μ°. POSTλ μ¬μ€μ λͺ¨λ μ§ ν  μ μλ€. 


---


## 3. PUT λ©μλ
```
PUT /members/100 HTTP/1.1 
Content-Type: application/json
{
	"username": "hello",
	"age": 20
}
```

PUT λ©μλλ λ¦¬μμ€ μ μ²΄λ₯Ό λμ²΄νλ€. κΈ°μ‘΄ λ¦¬μμ€κ° μμ κ²½μ° μλ‘ μμ±νλ€. μ¦, λ?μ΄μ°κΈ°λ₯Ό μννλ€κ³  λ³Ό μ μλ€. POSTμ μ°¨μ΄μ μ ν΄λΌμ΄μΈνΈκ° λ¦¬μμ€μ μμΉλ₯Ό μκ³  URIλ₯Ό λͺμν΄μΌ νλ€λ μ μ΄λ€.

> **λ¦¬μμ€κ° μ΄λ―Έ μ‘΄μ¬νλ κ²½μ°, κΈ°μ‘΄ λ°μ΄ν°λ₯Ό λμ²΄νλ€.**
![](https://images.velog.io/images/woply/post/199c1006-975a-4a56-8786-dae5898f62d9/image.png) ![](https://images.velog.io/images/woply/post/cbff756f-e987-4a3e-aecc-5271addcdcfc/image.png)

> **λ¦¬μμ€κ° μλ κ²½μ°, μλ‘μ΄ λ°μ΄ν°λ₯Ό μμ±νλ€.**
![](https://images.velog.io/images/woply/post/476720be-9164-407a-814d-d5c9944e139d/image.png) ![](https://images.velog.io/images/woply/post/0801c8f6-b6e1-4f94-9489-cb52acb4154e/image.png)

> **μ£Όμν  μ μ λ¦¬μμ€λ₯Ό μμ ν λμ²΄νλ€λ κ²μ΄λ€. λΆλΆμ μΈ μμ μ΄ λΆκ°λ₯νλ€.**
![](https://images.velog.io/images/woply/post/5184b672-f62e-455a-b98e-6b8dfb9ecfd0/image.png) ![](https://images.velog.io/images/woply/post/9d6c94af-c6f8-4c6e-adb1-1b5d18c939ae/image.png)


---

## 4. PATCH λ©μλ
```
PATCH /members/100 HTTP/1.1 
Content-Type: application/json
{
	"age": 50
}
```

PATCH λ©μλλ λ¦¬μμ€λ₯Ό λΆλΆ λ³κ²½νλ€. λΆλΆ λ³κ²½μ΄ νμν μν©μμ PATCHλ₯Ό μ¬μ©ν  μ μλ€λ©΄ POSTλ₯Ό μ¬μ©νλ€.

![](https://images.velog.io/images/woply/post/8480b33c-dd11-4594-80cf-b30757e0b97c/image.png)
![](https://images.velog.io/images/woply/post/3b9c6f66-9e24-4944-bd68-b19d95468517/image.png)


---


## 5. DELETE λ©μλ
```
DELETE /members/100 HTTP/1.1 
Host: localhost:8080
```
DELETE λ©μλλ λ¦¬μμ€λ₯Ό μ κ±°νλ€.

![](https://images.velog.io/images/woply/post/2efb3d45-5231-45cc-909a-fad0554da63b/image.png)
![](https://images.velog.io/images/woply/post/f8f4874e-244b-4cf2-960d-3b9f620bf969/image.png)



