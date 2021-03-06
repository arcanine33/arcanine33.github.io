---
title: "[Book]Clean Architecture 6부(30~32장)"
categories: book
toc: true
toc_label: "목차"
toc_icon: "cog"
---

## 데이터 베이스는 세부사항이다

- 데이터 베이스는 세부사항이라서 아키텍처의 구성요소 수준으로 끌어올릴 수 없다.

### 관계형 데이터베이스

- 관계형 테이블은 특정한 형식의 데이터에 접근하는 경우에는 편리하지만, 데이터를 테이블에 행 단위로 배치한다는 자체는 아키텍처적으로 볼 때 전혀 중요하지 않다.
- 많은 데이터 접근 프레임워크가 테이블과 행이 객체 형태로 시스템 여기저기에서 돌아다니게 허용하는데, 아키텍처적으로 잘못된 설계다. 이렇게 하면 USE-CASE, 업무 규칙, 심지어 UI조차도 관계형 데이터 구조에 결합 되어 버린다.

## 웹은 세부사항이다

- GUI는 세부사항이며 웹은 GUI다. 따라서 웹은 세부사항이다.

## 프레임워크는 세부사항이다

- 프레임워크를 사용할 수는 있지만, 결합해서는 안 된다.
- 프레임워크를 아키텍쳐 바깥쪽 원에 속하는 세부사항으로 취급하라.