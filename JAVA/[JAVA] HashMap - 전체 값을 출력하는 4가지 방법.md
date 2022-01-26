> HashMap을 사용하며 Map에 저장된 전체 값을 출력하는 다양한 방법을 학습하고 정리하였다. 



# HashMap - 전체 값을 출력하는 방법

**1. map.entrySet()**
**2. map.keySet() & map.get()**
**3. map.keyValue()**
**4. forEach**
 

![](https://images.velog.io/images/woply/post/ff1f2f08-28fa-4e96-888c-cdf6132809b5/image.png)
> 참고 이미지: Map의 상속 구조

<br>

---

## 1. map.entrySet()


```java
public Set<Map.Entry<K,V>> entrySet() // set 형태로 Entry를 얻는다
```

map.entrySet() 메소드는 key, value를 가진 전체 Entry를 Set 객체로 반환한다.
for문을 이용하여 Entry 객체 안의 key와 value를 사용할 수 있다.

```java
Map<Integer, String> map = new HashMap<Integer, String>();
map.put(1, "A");
map.put(2, "B");	
map.put(3, "C");

for (Entry<Integer, String> entrySet : map.entrySet()) {	
	System.out.println(entrySet.getKey() + " : " + entrySet.getValue());	
}

// 결과
// 1: A
// 2: B
// 3: C 

```

---

## 2. map.keySet() & map.get()


```java
public Set<K> keySet() // Set 형태로 전체 key 값을 얻는다
public V get(Object key) // key 값에 해당하는 value를 얻는다
```
keySet()과 get()를 복합 사용하는 방법이다.
keySet() 메서드는 map이 가지고 있는 전체 key를 모아 Set 형태로 반환한다.
get() 메서드는 매개변수로 key를 전달하면 해당하는 value를 반환한다.
 
  
``` java
Map<Integer, String> map = new HashMap<Integer, String>();
map.put(1, "A");
map.put(2, "B");	
map.put(3, "C");

Set<Integer> keySet = map.keySet();
for (Integer key : keySet) {	
	System.out.println(key + " : " + map.get(key));	
}

// 결과
// 1: A
// 2: B
// 3: C 

```

---

## 3. map.values()

```java
public Collection<V> values() // Collection 형태로 value를 얻는다.
```

values()는 map이 가지고 있는 value를 Collection 형태로 반환한다.
values()는 key와 value를 모두 얻을 수 있는 다른 방법들과 value 값만 얻을 수 있다.
 

```java
Map<Integer, String> map = new HashMap<Integer, String>();
map.put(1, "A");
map.put(2, "B");	
map.put(3, "C");

Collection<String> values = map.values();
System.out.println(values);

// 결과
// [A, B, C]

```

---

## 4. map.forEach()


forEach는 Java 8 버전 이후 추가된 기능이다. map이 가진 key와 value를 순차적으로 거친다.




```java
Map<Integer, String> map = new HashMap<Integer, String>();
map.put(1, "A");
map.put(2, "B");	
map.put(3, "C");

map.forEach((key, value) -> {	
	System.out.println(key + " : " + value);	
});	

// 결과
// 1: A
// 2: B
// 3: C 

```

---

