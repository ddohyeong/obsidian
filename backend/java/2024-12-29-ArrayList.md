---
title: ArrayList
categories:
  - java
tags: 
toc: true
toc_sticky: true
date: 2024-12-29
posting: false
---
[[2024-12-21-제네릭]]
[[2024-12-28-와일드카드]]

## Array
-  **조회** : $O(1)$ 만에 데이터를 조회 가능함, 단순하지만 매우 효율적인 자료구조
-  **검색** : 배열의 데이터를 찾는 행위 $O(N)$
-  **추가** : 배열의 위치에 따른 처리 속도가 다름, 마지막에 넣는 게 O(1) 로 제일 빠름 
	- 첫번째나 사이에 넣으면 O(n) , 이유는 한칸씩 데이터를 밀고 새로운 데이터를 넣기 때문

## 배열과 리스트
- 배열 : 순서가 있고 중복을 허용하지만 크기가 정적으로 고정
- 리스트 : 순서가 있고 중복을 허용하지만 크기가 동적으로 변화

### 배열을 이용한 리스트 구현
#### MyArrayList1
```java
public class MyArrayListV1 {  
    private static final int DEFAULT_CAPACITY = 5;  
  
    private Object[] elementData;  
    private int size = 0;  
  
    public MyArrayListV1() {  
        elementData = new Object[DEFAULT_CAPACITY];  
    }  
  
    public MyArrayListV1(int initialCapacity) {  
        elementData = new Object[initialCapacity];  
    }  
  
    public int size() {  
        return size;  
    }  
  
    public void add(Object object) {  
        elementData[size] = object;  
        size++;  
    }  
  
    public Object get(int index) {  
        return elementData[index];  
    }  
  
    public Object set(int index, Object element) {  
        Object oldValue = get(index);  
        elementData[index] = element;  
        return oldValue;  
    }  
  
    public int indexOf(Object o) {  
        for (int i = 0; i < size; i++) {  
            if (o.equals(elementData[i])) {  
                return i;  
            }  
        }  
        return -1;  
    }  

    public String toString() {  
        return Arrays.toString(Arrays.copyOf(elementData, size))  
                + " size=" + size + ", capacity=" + elementData.length;  
    }  
}
```
배열을 이용하여 `ArrayList`를 직접 구현 과정이다. 

#### MyArrayList2
```java
public void add(Object object) {  
        // 코드 추가  
        if (size == elementData.length) {  
            grow();  
        }  
  
        elementData[size] = object;  
        size++;  
    }  
  
    private void grow() {  
        int oldCapacity = elementData.length;  
        int newCapacity = oldCapacity * 2;  
        // 배열을 새로 만들고 기존 배열을 새로운 배열에 복사  
        elementData =  Arrays.copyOf(elementData, newCapacity);  
	}
```
`ArrayList` 를 구현하는 과정에서 배열 size가 Capacity 까지 증가하면 기존 Capacity의 2배를 더 크게 해서 사이즈를 배열을 복사하는 과정
<br>
이때 늘려주고 참조를 바꿔준 후 남은 배열은 `GC`에 의해 삭제된다. 
그리고 보통 자바에서는 메모리의 크기를 적절히 하기 위해 `50%` 정도 크기를 늘린다고 한다. 

#### MyArrayList3
**배열 리스트 BigO**
- 데이터 추가 : O(N) , 마지막 추가 시 O(1)
- 데이터 삭제 : O(N), 마지막 삭제 시 O(1)
- 인덱스 조회 : O(1)
- 데이터 검색 : O(N) 

배열 리스트는 데이터를 중간에 추가, 삭제보다는 데이터를 순서대로 입력하고 순서대로 출력하는 경우에 가장 효율적인 자료구조이다. 

#### MyArrayList4
이전까지 배열에 데이터를 넣을때 `Object` 를 이용했기 때문에 값을 꺼내어 다운 캐스팅 시 문제가 발생할 수 있었다. 이를 해결하기 위해 제네릭을 활용하여 타입에 대한 문제를 해결한다. 

```java
public class MyArrayListV4<E> {
	private static final int DEFAULT_CAPACITY = 5;  
	  
	private Object[] elementData;
...

	@SuppressWarnings("unchecked")
    public E get(int index) {
        return (E) elementData[index];
    }
}
```

위와 같이 제네릭을 활용하면 타입 안정성이 높은 코드를 만들 수 있다. 

#### MyArrayList5

```java
new Objcet[DEFAULT_CAPACITY];
```

이 Object에 제네릭을 사용하지 못하는 이유는 런타임에 이레이저에 의해 타입정보가 사라지기 때문이다. 

> 🧐 `new Object`를 사용해도 문제가 없는지?

타입 인자 적용 후 
```java
Object[] elementData;

void add(String e){
	elementData[size] = e;
}

E get(int index){
	return (String) elementData[index];
}
```

`MyArryListV4` 의 코드에서 String 으로 타입 변환이 이루어지면 add 시 String 타입으로만 데이터가 입력되고 get 에서도 String으로만 다운캐스팅 되기 때문에 문제가 되지 않는 것을 알 수 있다. 

### MyArrayList의 단점
배열을 사용한 리스트인 MyArrayList는 단점이 존재한다. 
- 정확한 크기를 알지 못하면 메모리가 낭비된다, 배열을 사용하므로 배열 뒷 부분에 사용되지 않고 낭비 된다.
- 데이터를 중간에 추가하거나 삭제할 때 비효율적이다. 

이러한 단점을 해결하기 위해 `Linked List`를 활용하도록 하자. 


> 📍 Ref.
1.  김영한님의 실전자바-중급 2편
