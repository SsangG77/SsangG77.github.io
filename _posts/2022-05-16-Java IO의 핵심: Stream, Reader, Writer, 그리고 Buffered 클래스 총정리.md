---
title: Java IO의 핵심: Stream, Reader/Writer, 그리고 Buffered 클래스 총정리
date: 2022-05-16 12:00:00 +0900
categories: [개념, JAVA]
tags: [stream, buffer]
---




## 1. Stream vs Reader/Writer

| 구분     | Stream                        | Reader/Writer      |
| ------ | ----------------------------- | ------------------ |
| 처리 단위  | 1 byte                        | 2 byte (문자 단위)     |
| 용도     | 바이너리 데이터 (이미지, 영상, 오디오 등)     | 텍스트 데이터 (문자열 등)    |
| 클래스 예시 | `InputStream`, `OutputStream` | `Reader`, `Writer` |

---

## 2. 스트림(Stream)의 개념

**Stream**이란 데이터의 흐름을 추상화한 개념이다. 외부에서 들어오거나 외부로 나가는 데이터를 통로처럼 다룰 수 있게 해준다. 배열이나 컬렉션처럼 반복적으로 데이터를 다룰 수 있으며, 중간 연산과 최종 연산으로 구분된다.

### 중간 연산

* `filter`: 조건에 맞는 요소 필터링

```java
sList.stream().filter(s -> s.length() >= 5).forEach(System.out::println);
```

* `map`: 특정 필드를 추출

```java
customerList.stream().map(Customer::getName).forEach(System.out::println);
```

### 최종 연산

* `forEach`, `count`, `sum`, `reduce` 등
* 스트림은 최종 연산이 수행되면 소모된다. 재사용 불가.

---

## 3. Input/OutputStream (Byte 기반)

### InputStream

키보드, 파일, 네트워크에서 1바이트씩 읽는다.

```java
InputStream is = System.in;
int inputData = is.read();
System.out.println((char) inputData);
```

### InputStreamReader

1바이트 단위로 읽은 데이터를 문자로 변환해 읽는다.

```java
InputStreamReader isr = new InputStreamReader(System.in);
int inputData = isr.read();
System.out.println((char) inputData);
```

### OutputStream

1바이트씩 출력.

```java
OutputStream os = System.out;
os.write((char) input);
```

### OutputStreamWriter

문자열을 바이트로 변환해 출력.

```java
OutputStreamWriter osw = new OutputStreamWriter(System.out);
osw.write(arr);
```

---

## 4. File 입출력 (파일 기반 IO)

### FileInputStream

파일에서 바이트 단위로 읽기

```java
FileInputStream fis = new FileInputStream("javaio.txt");
int data = fis.read();
```

### FileOutputStream

파일에 바이트 단위로 쓰기

```java
FileOutputStream fos = new FileOutputStream("new.txt");
fos.write(data);
```

### FileReader / FileWriter

문자 단위 파일 입출력

```java
FileReader fr = new FileReader("javaio.txt");
FileWriter fw = new FileWriter("new.txt", true);
```

---

## 5. Buffered Stream (성능 최적화)

버퍼는 데이터를 일정량 모아서 한 번에 처리해 입출력 성능을 높인다. 보조 스트림으로, 주 스트림과 함께 사용한다.

### BufferedInputStream

```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("javaio.txt"));
```

### BufferedReader

문자열을 라인 단위로 읽음

```java
BufferedReader br = new BufferedReader(new FileReader("javaio.txt"));
String line = br.readLine();
```

### BufferedOutputStream

버퍼에 저장 후 `flush()`로 한 번에 출력

```java
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("alpha.txt"));
bos.write('a');
bos.flush();
```

### BufferedWriter

텍스트를 라인 단위로 저장

```java
BufferedWriter bw = new BufferedWriter(new FileWriter("new3.txt"));
bw.write("자바");
bw.flush();
bw.newLine();
```

---

## 마무리

* Stream은 바이트 기반, Reader/Writer는 문자 기반.
* 보조 스트림(Buffer)은 성능 최적화를 위해 사용.
* File 관련 IO는 목적에 따라 FileInput/OutputStream 또는 FileReader/Writer 선택.
* 항상 스트림은 사용 후 `close()` 호출로 자원 해제해야 함.

