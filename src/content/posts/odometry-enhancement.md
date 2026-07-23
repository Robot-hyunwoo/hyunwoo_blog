---
title: "Odometry Enhancement — Encoder 기반 Odometry 정확도 개선"
published: 2026-07-23T10:30:00+09:00
description: "DD·QD 타입 로봇의 Odometry 정확도 개선 — Wheel Encoder 기반 이동량 산출과 Overflow 보정으로 평균 위치 오차 약 80% 감소 (5.2cm → 1.1cm)"
image: "/posts/odometry-enhancement/cover.png"
tags: [Localization, Odometry, Encoder, Sensor]
category: Project
draft: false
lang: ko
---

## 0️⃣ 프로젝트 개요

- **프로젝트명**: Odometry 정확도 개선 프로젝트
- **프로젝트 기간**:
  - **1차** (프로토 개발): 2022년 1월 - 2022년 3월
  - **2차** (DD, QD 타입 적용 및 개선): 2024년 9월 - 2024년 11월
- **프로젝트 목적**: DD, QD Type Odometry 정확도를 개선하고 Encoder를 적용한 Odometry 산출 및 적용
- **팀 구성**: 2명 (ROS 개발자 1명, 모터 드라이버 제어 개발자 1명)

## 1️⃣ 역할 및 담당 업무

- **역할**: ROS 개발자
- **담당 업무**:
  - Encoder 기반 Odometry 알고리즘 설계 및 개선
  - Motor-PC 간 통신 프로토콜 설계 및 구현
  - Optitrack 기반 정확도 검증 및 현장 적용

## 2️⃣ 기술 스택

| 분류 | 기술 | 활용 내용 |
|------|------|-----------|
| **환경** | ROS1 Melodic / Ubuntu 18.04 | 로봇 미들웨어 및 개발 환경 구성 |
| **언어** | C++, Python | Odometry Controller 코드 구현 및 Python 프로토콜 테스트 코드 작성 |
| **핵심 패키지** | 자체 개발 | 자체 개발 Odometry 패키지 |
| **센서** | Wheel Encoder | Wheel Encoder를 통한 모터 회전량 측정 |
| **도구** | CMake, RViz, Optitrack | 빌드 시스템 구성, 시각화 및 정밀 위치 측정 |

## 3️⃣ Trouble-Shooting

> [!WARNING]
> **[문제 상황] Encoder Data Overflow로 인한 위치 점프**

- **원인 분석**
  - 모터 엔코더 데이터를 4byte 정수형(int32)으로 수신
  - 누적값이 최대치(±2³¹)를 초과하면 Overflow가 발생하여 부호가 반전됨
  - 반전된 데이터로 위치를 계산하면 급격한 위치 변화로 인식되어 로봇 위치가 점프
- **해결 방법**
  - 이전 값과 현재 값의 차이(delta)를 계산할 때, Overflow 경계를 고려한 보정 로직 적용
  - 차이값이 비정상적으로 클 경우 Overflow로 판단하고 정상 delta로 변환
- **결과**
  - Overflow 발생 시에도 정상적인 이동량 산출, 위치 점프 현상 해결

## 4️⃣ 결과 및 성장

**정량적 성과**

- Overflow 발생 시에도 정상적인 이동량 산출, 위치 점프 현상 해결
- 기존 Velocity 기반 대비 Encoder 기반 Odometry 적용 시 평균 위치 오차 약 80% 감소 (X: 3.5cm → 0.7cm, Y: 3.8cm → 0.8cm)
- 종합 정확도 기준 5.2cm에서 1.1cm로 개선

**정성적 성장**

- CAN 통신 프로토콜 분석 및 bit 단위 데이터 파싱 기법을 습득하여 Motor-PC 간 저수준 통신 구현 역량 확보
- Optitrack을 활용한 Ground Truth 데이터 수집 및 정량적 성능 평가 방법론 습득

## 5️⃣ 데모 및 자료

**Encoder vs Velocity 위치 정확도 측정 결과**

![Encoder 기반과 Velocity 기반 Odometry의 위치 정확도 비교](/posts/odometry-enhancement/encoder-vs-velocity.png)
