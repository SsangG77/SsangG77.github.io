---
title: JadenCase 문자열 만들기
date: 2025-04-03 12:00:00 +0900
categories: [코딩테스트]
tags: []
---



### **문제 설명**

JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 단, 첫 문자가 알파벳이 아닐 때에는 이어지는 알파벳은 소문자로 쓰면 됩니다. (첫 번째 입출력 예 참고)

문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.

### 제한 조건

- s는 길이 1 이상 200 이하인 문자열입니다.
- s는 알파벳과 숫자, 공백문자(" ")로 이루어져 있습니다.
    - 숫자는 단어의 첫 문자로만 나옵니다.
    - 숫자로만 이루어진 단어는 없습니다.
    - 공백문자가 연속해서 나올 수 있습니다.

### 입출력 예

| s | return |
| --- | --- |
| "3people unFollowed me" | "3people Unfollowed Me" |
| "for the last week" | "For The Last Week" |

<aside>

### 생각 흐름

1. 문자열을 하나씩 확인한다.
2. 첫번째 문자열이 숫자거나 공백이면 result 변수에 그냥 할당하고, 아니면 대문자로 할당한다.
3. 그 다음부터는 result 마지막 값이 공백이면 대문자로 변경해서 넣고, 아니면 소문자로 넣는다.
</aside>

<aside>

### 코드

</aside>


```swift

func solution(_ s:String) -> String {
     
     
     var result = ""
     
     for i in 0...s.count-1 {
         let index = s.index(s.startIndex, offsetBy: i)
         
         if i == 0 { // 첫번째일때
             if s[index].isNumber || s[index] == " " { //숫자거나 공백이면 넣기
                 result += String(s[index])
             } else {
                 result += String(s[index]).uppercased() // 아니면 대문자로 넣기
             }
         } else { // 첫번째가 아닐때
             if let last = result.last { // result 마지막 값
                 if last == " " { // result 마지막 값이 공백일때
                     result += String(s[index]).uppercased()
                 } else {
                     result += String(s[index]).lowercased()
                 }
             }
         }
     }
    
    return result

}

```



