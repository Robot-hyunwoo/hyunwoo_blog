---
title: "IMU Calibration — Mount 보정·Gyro Calibration 및 Slip 감지"
published: 2026-07-23T11:30:00+09:00
description: "IMU Mount 오차 보정과 Bias/Gyro Calibration으로 정제된 IMU 데이터 산출 — Deadzone 적용과 Encoder 융합 기반 Slip 감지로 제자리 회전 오차 약 20% 감소"
image: "/posts/imu-calibration/cover.png"
tags: [Sensor, IMU, Calibration, SlipDetection]
category: Project
draft: false
lang: ko
---

## 0️⃣ 프로젝트 개요

- **프로젝트명**: IMU Mount Calibration 기능 개발 및 Gyro 보정 데이터 산출
- **프로젝트 기간**: 2025.10 ~ 2025.12
- **프로젝트 목적**: IMU Mount 오차 보정 및 Bias/Gyro Calibration을 통한 정제된 IMU 데이터 산출
- **팀 구성**: 1명 (ROS 개발자, 단독 수행)

## 1️⃣ 역할 및 담당 업무

- **역할**: ROS 개발자
- **담당 업무**:
  - IMU Mount Calibration 기능 개발
  - 초기 부팅 시 Bias 수집 및 자동 보정 기능 개발
  - Gyro Deadzone 적용을 통한 정제된 Orientation 산출 기능 구현
  - 정제된 IMU 데이터와 Encoder 데이터를 통한 구동축 Slip 감지 및 보정 기능 구현

## 2️⃣ 기술 스택

| 분류 | 기술 | 활용 내용 |
|------|------|-----------|
| **환경** | ROS1 Melodic / Ubuntu 18.04 | 로봇 미들웨어 및 개발 환경 구성 |
| **언어** | C++ | IMU Calibration 기능 전체 파이프라인 구성 |
| **핵심 패키지** | imu_tk | 오픈소스 기반 Calibration 알고리즘 구현 |
| **센서** | IMU | Calibration 대상 센서 데이터 수집 및 검증 |
| **도구** | CMake, RViz, CLion | 빌드 시스템 구성 및 실시간 TF / Pose 시각화 검증 |

## 3️⃣ Trouble-Shooting

> [!WARNING]
> **[문제 상황] 저속 주행 시 IMU Steady State 판정으로 인한 위치 업데이트 중단**

- **원인 분석**
  - 저속에서 IMU 정지 상태 판별 기능에 의해 위치 계산 중단
  - Bias 갱신 시 위치 오차 누적 발생
- **해결 방법**
  - 초기 정지 상태에서 Bias 수집 후 보정 적용
  - Deadzone 적용으로 Bias/Gyro 노이즈 필터링
- **결과**
  - 저속 환경에서도 정상적인 위치 계산 수행
  - Gyro 기반 회전 정확도 향상

## 4️⃣ 결과 및 성장

**정량적 성과**

- IMU + Wheel Odometry 통합 시스템 구현
- 제자리 회전 오차 약 20% 감소
- Slip 감지 기능 신규 개발로 위치 추정 강건성 확보

**정성적 성장**

- IMU Calibration 파이프라인 설계 및 구현 경험 습득
- Accel/Gyro 데이터 필터링 및 보정 기법 습득

## 5️⃣ 데모 및 자료

**IMU Data Calibration Result**

![IMU Mount Calibration 전후 비교](/posts/imu-calibration/mount-cal-compare.jpg)

**Slip Detection Demo**

<!-- VIDEO_PENDING: /videos/slip-detection-demo.webm (노션 첨부 slip_detection_demo.webm) -->
> 데모 영상 준비 중입니다.
