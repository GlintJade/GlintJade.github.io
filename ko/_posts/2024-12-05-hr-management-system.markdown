---
# multilingual page pair id, this must pair with translations of this page. (This name must be unique)
lng_pair: id_hr_management_system
title: "인사 관리 시스템 - Oracle 트리거로 급여 데이터 자동화하기"

# post specific
# multiple category is not supported
category: 프로젝트
# multiple tag entries are possible
tags: [데이터베이스, Oracle, Spring, JDBC, 트리거, 팀 프로젝트]
# thumbnail image for post
img: "/assets/img/projects/hrms.png"

# publish date
date: 2024-12-05 18:00:00 +0900
---

<!-- outline-start -->

데이터베이스프로그래밍 수업에서 3인 팀으로 **인사 관리 웹 시스템**을 만들었습니다. 직원 정보와 출퇴근 기록, 급여를 한곳에서 관리하고, 반복되는 계산은 Oracle 저장 프로시저와 트리거가 자동으로 처리하도록 설계했습니다. 저는 DB 설계와 백엔드, Oracle 환경 구축, 그리고 급여 관련 트리거 2개를 맡았습니다.

<!-- outline-end -->

![인사 관리 대시보드](:hrms-dashboard.png){:data-align="center"}

### 프로젝트 개요

| 항목 | 내용 |
| --- | --- |
| 기간 | 2024.10.17 ~ 2024.12.05 |
| 인원 | 3인 (데이터베이스프로그래밍 팀 프로젝트) |
| 역할 | DB 설계, 웹 백엔드, Oracle 환경 구축, 트리거 작성 |
| 기술 스택 | Oracle, Spring Framework, Java, JDBC, React |

인사 데이터를 수작업으로 관리하면 시간이 많이 들고 계산 실수나 누락이 생기기 쉽습니다. 그래서 출퇴근 시간을 입력하면 근무 시간이 자동으로 계산되고, 직원 등록이나 인센티브 지급 같은 변경이 생기면 급여 데이터도 함께 갱신되는 시스템을 목표로 했습니다.

### 주요 기능

- **대시보드**: 전체 사원 수, 직급별·부서별 사원 수, 매출 상위 3개 부서를 한 화면에 보여줍니다. 저장 프로시저로 통계를 계산해 가져옵니다.
- **부서/직원 관리**: 직원 등록, 직원 정보 조회, 부서별 직원 목록을 제공합니다. 부서별 직원 목록에는 입사일 기준 근무일수가 함께 표시됩니다.
- **출퇴근 관리**: 출근·퇴근 시간을 등록하면 하루 근무 시간과 월별 근무 시간이 자동으로 계산됩니다.
- **급여 관리**: 직원별 급여를 조회하고, 인센티브를 등록하면 월급에 바로 반영됩니다.

### 데이터베이스 설계

직원, 직급, 부서, 급여, 출퇴근 기록을 중심으로 설계했습니다. 1차 보고서 이후 교수님의 피드백을 반영해 직급에 직급별 급여를, 부서에 매출 총액을, 급여에 인센티브를 추가하는 등 ERD와 릴레이션 스키마를 수정했습니다. 저는 릴레이션 스키마 작성을 맡았습니다.

![E-R 다이어그램](:hrms-erd.png){:data-align="center"}

### 내가 맡은 부분

#### 1. 직원 등록 시 급여 데이터 자동 생성 트리거

직원과 급여는 1:1 관계로 서로를 외래키로 참조합니다. 그래서 직원만 등록하고 급여 데이터가 없으면 참조 무결성이 깨집니다. 직원이 등록되면 해당 직급의 기본급을 가져와 급여 레코드를 자동으로 만드는 `INSERT_EMPLOYEE_CREATEPAY` 트리거를 작성했습니다. 연봉은 기본급의 12배로 계산합니다.

![INSERT_EMPLOYEE_CREATEPAY 트리거](:hrms-trigger-createpay.png){:data-align="center"}

웹 화면에서 직원 ID 201, 직급 ID 203인 직원을 등록해 트리거가 동작하는지 확인했습니다.

![직원 등록 화면](:hrms-employee-register.png){:data-align="center"}

등록 전 급여(PAY) 테이블에는 직원 201의 급여 데이터가 없습니다.

![등록 전 급여 테이블](:hrms-pay-before.png){:data-align="center"}

등록 후에는 트리거가 실행되어 직원 201의 급여 데이터(PAY_ID 263)가 자동으로 추가되었습니다. 기본급은 직급 203의 급여인 3,500,000원, 연봉은 그 12배인 42,000,000원으로 계산되었습니다.

![등록 후 급여 테이블](:hrms-pay-after.png){:data-align="center"}

#### 2. 인센티브 등록 시 월급 갱신 트리거

급여 테이블에 인센티브가 등록되면 직원 테이블의 월급에 기본급과 인센티브를 더해 반영하는 `UPDATE_EMPLOYEE_MONTHPAY` 트리거를 작성했습니다. 인센티브가 아직 없는(NULL) 경우에도 계산이 깨지지 않도록 `NVL`로 0을 넣어 처리했습니다.

![UPDATE_EMPLOYEE_MONTHPAY 트리거](:hrms-trigger-monthpay.png){:data-align="center"}

#### 3. 백엔드와 개발 환경

- Spring과 JDBC로 출퇴근 기록, 부서, 직원 관련 API를 구현했습니다.
- 팀원들이 같은 DB로 작업할 수 있도록 Oracle 개발 환경 구축에 참여했습니다.
- 프론트엔드와 연동하기 전에 입력과 조회 흐름을 확인할 수 있도록 임시 Thymeleaf 화면을 만들었습니다.
- 보고서와 회의록 전체를 검토하고 보완했습니다.

### 결과와 아쉬운 점

설계 목표로 잡은 DB 설계, 인적자원 통계, 급여 계산, 근무일수·근무시간 계산, DB 연동, GUI를 모두 구현했습니다. 다만 사내 인사 정보를 다루는 시스템인데 로그인 없이 접근할 수 있게 만든 점이 아쉬웠습니다. 다음에는 회원가입과 로그인으로 접근 권한을 관리해 보안을 더 신경 쓰고 싶습니다.

### 링크

- [GitHub 작업 브랜치](https://github.com/haeing0v0/DBP_4Team/tree/back_mk)
- [내 커밋 내역](https://github.com/haeing0v0/DBP_4Team/commits/back_mk/?author=GlintJade)
