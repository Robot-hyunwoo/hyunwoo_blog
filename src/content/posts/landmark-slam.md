---
title: "Landmark SLAM — Camera Landmark 기반 위치 보정 SLAM"
published: 2026-07-23T10:15:00+09:00
description: "카메라로 인식한 AprilTag/QR Landmark를 2D SLAM에 통합해 위치추정 강건성 확보 — Landmark Covariance 산출 로직으로 Pose Graph Loop Closure 최적화 구현"
image: "/posts/landmark-slam/cover.jpg"
tags: [SLAM, Landmark, AprilTag, Camera]
category: Project
draft: false
lang: ko
---

## 0️⃣ 프로젝트 개요

- **프로젝트명**: Camera로 인식한 Landmark를 활용한 SLAM 개발
- **프로젝트 기간**:
  - **1차** (3D Landmark SLAM): 2023.06 - 2023.11
  - **2차** (바닥 QR Landmark SLAM): 2025.08 - 2025.10
- **프로젝트 목적**: 카메라 기반 Landmark를 2D SLAM에 통합하여 위치 보정 기능 개발 및 위치추정 강건성 확보
- **팀 구성**: 2명 (ROS SLAM 개발자 2명)

## 1️⃣ 역할 및 담당 업무

- **역할**: ROS SLAM 개발자
- **담당 업무**:
  - 카메라 기반 AprilTag/QR 인식 및 Landmark 데이터 수집 기능 구현
  - Landmark 정보가 포함된 2D SLAM 맵 저장 기능 구현
  - 시뮬레이션 검증 및 실환경 적용 테스트

## 2️⃣ 기술 스택

| 분류 | 기술 | 활용 내용 |
|------|------|-----------|
| **환경** | ROS1 Melodic / Ubuntu 18.04 | 로봇 미들웨어 및 개발 환경 구성 |
| **언어** | C++ | Landmark SLAM, apriltag_ros 구현 및 커스텀 |
| **핵심 패키지** | SR-SLAM, Apriltag ROS | SR-SLAM 기반 Landmark SLAM 구현 |
| **센서** | Camera, 2D LiDAR, Wheel Odometry | Landmark 생성을 위한 카메라 데이터 및 이동량 데이터 제공 |
| **도구** | CMake, RViz, CLion | 빌드 시스템 구성 및 3D Landmark 시각화 |

## 3️⃣ Trouble-Shooting

> [!WARNING]
> **[문제 상황] Landmark Loop Closure 시 Solver 계산 불가**

- **원인 분석**
  - 카메라 기반 Landmark에 Covariance 계산 로직이 부재
  - Covariance 없이 Pose Graph에 등록되어 Solver 최적화 불가
- **해결 방법**
  - 인식 거리, 횟수 및 클러스터링 기반으로 Covariance 산출 로직 개발
  - Scan Match 결과와 클러스터 평균 Pose 간 차이를 활용한 Covariance 계산 적용
- **결과**
  - Solver에서 Landmark를 정상적으로 최적화 계산에 사용
  - 2D SLAM과 동일하게 Loop Closure 동작 및 맵 정합 완료

## 4️⃣ 결과 및 성장

**정량적 성과**

- Landmark 기반 Loop Closure를 2D SLAM과 동일한 방식으로 통합 구현
- 2D LiDAR만으로 위치 추정이 실패하던 구간에서 Landmark 기반 위치 복구 성공

**정성적 성장**

- 센서 입력 및 위치 추정값에 대한 Covariance 설계의 중요성 재확인
- 카메라 센서 활용 시 노이즈 필터링 등 후처리 로직의 필요성 습득

## 5️⃣ 데모 및 자료

**Landmark SLAM Mapping Demo**

<video controls muted preload="metadata" style="width:100%; border-radius:8px;">
  <source src="/videos/landmark-slam-demo.webm" type="video/webm" />
  브라우저가 video 태그를 지원하지 않습니다.
</video>

카메라로 인식한 Landmark를 2D SLAM 맵에 통합하는 실시간 맵핑 데모.
