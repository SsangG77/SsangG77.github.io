---
title: Navigation controller
date: 2025-03-07 12:00:00 +0900
categories: [개념, Swift]
tags: []
---


스택 기반 체계(예를 들면 브라우저의 뒤로가기)로 계층화된 내용을 탐색하기 위한 컨테이너 뷰 컨트롤러
하나 이상의 자식 컨트롤러가 존재해야함.

> Stack : LIFO(Last In First Out)로 역순 탐색을 위한 구조


![Image](https://github.com/user-attachments/assets/22b22715-452d-4b9d-b42d-f3591653c517)

네비게이션 컨트롤러 객체는 네비게이션 스택 이라고 하는 정렬된 배열을 사용하여 자식 뷰 컨트롤러를 관리합니다 . 

배열의 첫 번째 뷰 컨트롤러는 root view controller(가장 왼쪽)이며 스택의 맨 아래를 나타냅니다. 

배열의 마지막 뷰 컨트롤러(가장 오른쪽)는 스택의 맨 위 항목이며 현재 표시되고 있는 뷰 컨트롤러를 나타냅니다.
