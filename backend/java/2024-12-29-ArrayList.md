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

> 📍 Ref.
1.  김영한님의 실전자바-중급 2편
