---
title: JAVA Socket
date: 2022-06-28 12:00:00 +0900
categories: [개념, JAVA]
tags: [Socket]
---

# Java 소켓 프로그래밍으로 구현하는 간단한 채팅 프로그램

## 🎯 개요

네트워크 프로그래밍의 기초인 소켓(Socket) 통신을 이용해 Java로 간단한 채팅 프로그램을 구현해보자. 이 글에서는 TCP/IP 기반의 클라이언트-서버 구조를 통해 실시간 메시지 교환이 가능한 채팅 시스템을 만드는 과정을 단계별로 설명한다.

## 📡 소켓 통신의 기본 개념

### 소켓(Socket)이란?
소켓은 클라이언트와 서버 간의 통신을 가능하게 하는 네트워크 프로그래밍 인터페이스다. 클라이언트가 서버에 연결 요청을 보내면, 서버에서 해당 요청을 수락하고 소켓을 생성하여 양방향 통신이 가능해진다.

### 주요 구성 요소
- **포트 번호(Port Number)**: 서버가 특정 서비스를 제공하기 위해 사용하는 논리적 통로
- **ServerSocket**: 서버 측에서 클라이언트의 연결 요청을 대기하는 소켓
- **Socket**: 실제 데이터 통신이 이루어지는 소켓 객체

## 🏗️ 시스템 아키텍처

### 서버측 동작 구조
1. **서버소켓 생성**: 지정된 포트에서 클라이언트 요청 대기
2. **연결 승인**: `accept()` 메서드로 클라이언트 연결 요청 수락
3. **입출력 스트림 설정**: 소켓을 통한 데이터 송수신 준비
4. **메시지 처리**: 클라이언트로부터 메시지 수신 및 응답 전송
5. **자원 해제**: 통신 종료 후 모든 자원 정리

### 클라이언트측 동작 구조
1. **서버 연결**: IP 주소와 포트 번호를 통해 서버 접속
2. **입출력 스트림 설정**: 소켓을 통한 데이터 송수신 준비
3. **메시지 교환**: 사용자 입력을 서버로 전송하고 응답 수신
4. **연결 종료**: "bye" 명령어로 채팅 종료
5. **자원 해제**: 모든 스트림 및 소켓 정리

## 💻 서버 구현

```java
public class ServerEX {
    public static void main(String[] args) {
        ServerSocket server = null;
        Socket socket = null;
        
        BufferedReader br_in = null;
        BufferedWriter bw_out = null;
        
        Scanner sc = new Scanner(System.in);
        
        try {
            // 1. 서버소켓 생성 (포트 5454에서 대기)
            server = new ServerSocket(5454);
            System.out.println("서버 응답 대기중");
            
            // 2. 클라이언트 연결 승인
            socket = server.accept();
            System.out.println("연결되었습니다.");
            
            // 3. 입출력 스트림 설정
            br_in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            bw_out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
            
            // 4. 메시지 처리 루프
            while(true) {
                String in_msg = br_in.readLine();
                if(in_msg.equalsIgnoreCase("bye")) {
                    System.out.println("클라이언트가 채팅을 종료함.");
                    break;
                }
                System.out.println("client >> " + in_msg);
                System.out.print("server >> ");
                String out_msg = sc.nextLine();
                bw_out.write(out_msg + "\n");
                bw_out.flush();
            }
            
        } catch(Exception e) {
            e.printStackTrace();
        } finally {
            // 5. 자원 해제
            try {
                sc.close();
                bw_out.close();
                br_in.close();
                socket.close();
                server.close();
            } catch(Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 💻 클라이언트 구현

```java
public class Client {
    public static void main(String[] args) {
        Socket socket = null;
        BufferedReader br_in = null;
        BufferedWriter bw_out = null;
        Scanner sc = new Scanner(System.in);
        
        try {
            // 1. 서버 연결 (localhost:5454)
            socket = new Socket("127.0.0.1", 5454);
            
            // 2. 입출력 스트림 설정
            br_in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            bw_out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
            
            // 3. 메시지 교환 루프
            while(true) {
                System.out.print("client >> ");
                String out_msg = sc.nextLine();
                
                if(out_msg.equalsIgnoreCase("bye")) {
                    bw_out.write(out_msg + "\n");
                    bw_out.flush();
                    break;
                } else {
                    bw_out.write(out_msg + "\n");
                    bw_out.flush();
                    
                    String in_msg = br_in.readLine();
                    System.out.println("server >> " + in_msg);
                }
            }
        } catch(Exception e) {
            e.printStackTrace();
        } finally {
            // 4. 자원 해제
            try {
                sc.close();
                bw_out.close();
                br_in.close();
                socket.close();
            } catch(Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 🔧 핵심 기술 요소

### 1. BufferedReader/BufferedWriter 사용
- **목적**: 효율적인 입출력 처리를 위해 버퍼링 기능 활용
- **장점**: 한 번에 여러 문자를 처리하여 성능 향상
- **사용법**: `readLine()`으로 한 줄씩 읽기, `write()`로 데이터 전송

### 2. InputStreamReader/OutputStreamWriter
- **역할**: 바이트 스트림을 문자 스트림으로 변환
- **필요성**: 소켓의 기본 스트림은 바이트 단위, 문자 데이터 처리를 위해 변환 필요

### 3. flush() 메서드
- **기능**: 버퍼에 저장된 데이터를 즉시 전송
- **중요성**: 실시간 채팅을 위해 메시지 즉시 전달 보장

## ⚡ 실행 방법

1. **서버 실행**: `ServerEX.java` 컴파일 후 실행
2. **클라이언트 실행**: 서버 실행 후 `Client.java` 컴파일 후 실행
3. **채팅 시작**: 클라이언트에서 메시지 입력 후 엔터
4. **채팅 종료**: "bye" 입력으로 연결 종료

## 🚀 확장 가능성

이 기본적인 채팅 프로그램을 다음과 같이 발전시킬 수 있다:

- **멀티스레드 적용**: 여러 클라이언트 동시 접속 지원
- **GUI 인터페이스**: Swing 또는 JavaFX를 활용한 사용자 친화적 UI
- **파일 전송**: 텍스트 외 바이너리 데이터 전송 기능
- **보안 강화**: SSL/TLS를 통한 암호화 통신
- **데이터베이스 연동**: 채팅 기록 저장 및 사용자 관리

## 📝 마무리

Java 소켓 프로그래밍을 통해 간단한 채팅 프로그램을 구현해보았다. 이 예제는 네트워크 프로그래밍의 기초를 이해하고, 클라이언트-서버 아키텍처의 동작 원리를 학습하는 데 도움이 된다. 

소켓 통신의 핵심은 **연결 설정 → 데이터 교환 → 자원 해제**의 생명주기를 정확히 관리하는 것이다. 특히 자원 해제 부분은 메모리 누수 방지를 위해 반드시 `finally` 블록에서 처리해야 한다.

이 기초 지식을 바탕으로 더 복잡하고 실용적인 네트워크 애플리케이션을 개발할 수 있을 것이다.
