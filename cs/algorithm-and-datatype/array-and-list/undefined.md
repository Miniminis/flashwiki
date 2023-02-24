# 표준 라이브러리

## 정렬

```java
//오름차순
Collections.sort(list);
    
//내림차순
Collections.sort(list, Collections.reverseOrder());

//대소문자 구분없이 오름차순
Collections.sort(list, String.CASE_INSENSITIVE_ORDER);

// 대소문자 구분없이 내림차순
Collections.sort(list, Collections.reverseOrder(String.CASE_INSENSITIVE_ORDER));

// 오름차순으로 정렬
list.sort(Comparator.naturalOrder());
System.out.println("오름차순 : " + list);  // [A, B, C, a]

// 내림차순으로 정렬
list.sort(Comparator.reverseOrder());
System.out.println("내림차순 : " + list); // [a, C, B, A]

// 대소문자 구분없이 오름차순 정렬
list.sort(String.CASE_INSENSITIVE_ORDER);
System.out.println("대소문자 구분없이 오름차순 : " + list); // [a, A, B, C]

// 대소문자 구분없이 내림차순 정렬
list.sort(Collections.reverseOrder(String.CASE_INSENSITIVE_ORDER));
System.out.println("대소문자 구분없이 내림차순 : " + list); // [C, B, a, A]

```





