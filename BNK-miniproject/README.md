#  BNK 아쿠아리움 예약 시스템 (Mini Project)

> **"부산의 바다를 담다, 편리한 예약과 관람을 위한 아쿠아리움 웹 서비스"**  
> 실제 운영 중인 아쿠아리움 사이트를 벤치마킹하여, 회원가입부터 티켓 예매, 관리자 통계까지 구현한 웹 프로젝트입니다.

<br>

## Project Info
- **프로젝트 명:** BNK 아쿠아리움 (가칭)
- **개발 기간:** 2025.05.21 ~ 2025.05.27 (7일)
- **참여 인원:** 4명 (풀스택 개발)
- **주요 목표:** 
  - Spring Framework와 MyBatis를 활용한 안정적인 웹 애플리케이션 구축
  - 사용자 편의를 위한 직관적인 티켓 예매 및 발급 시스템 구현
  - Chart.js를 활용한 관리자 데이터 시각화

<br>

## Tech Stack

### Backend
<img src="https://img.shields.io/badge/Java-007396?style=for-the-badge&logo=java&logoColor=white"> <img src="https://img.shields.io/badge/Spring-6DB33F?style=for-the-badge&logo=spring&logoColor=white"> <img src="https://img.shields.io/badge/MyBatis-000000?style=for-the-badge&logo=fluentd&logoColor=white"> <img src="https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white">

### Frontend
<img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white"> <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white"> <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black">
<img src="https://img.shields.io/badge/JSTL-black?style=for-the-badge&logo=java&logoColor=white">

### Tools & Library
<img src="https://img.shields.io/badge/Chart.js-FF6384?style=for-the-badge&logo=chartdotjs&logoColor=white"> <img src="https://img.shields.io/badge/Eclipse-2C2255?style=for-the-badge&logo=eclipse&logoColor=white"> <img src="https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white">

<br>

## ERD & Database Logic(ERD 및 데이터베이스 로직)
<img width="1321" height="733" alt="ERD 이미지" src="https://github.com/user-attachments/assets/964d80bd-ac8c-4f5a-8dfc-84760ef8f366" />

**(주요 테이블 설계 로직)**

단순한 상품 구매를 넘어, 실제 티켓 사용 처리를 위해 **1:N 구조의 티켓 발급 시스템**을 설계했습니다.

1. **Items (상품)**: 아쿠아리움 입장권, 패키지 등 상품 정보
2. **Orders (주문)**: 고객이 결제한 내역 (주문번호 생성)
3. **Issued_Ticket (발급된 티켓)**: 
   - *Issue*: 수량 2개 구매 시, 주문 내역은 1개지만 입장권은 2개가 발급되어야 함
   - *Solution*: 주문(Orders) 테이블 하위에 개별 식별 번호(UUID 등)를 가진 **발급 티켓 테이블**을 별도로 설계하여 개별 사용 여부 관리

<br>

## Key Features(주요 특징)

| 구분 | 기능 | 설명 |
| --- | --- | --- |
| **회원** | 인증/인가 | **양방향 암호화** 로직을 적용한 보안 로그인 및 회원 정보 관리 |
| **티켓** | 예매/발급 | 상품 조회, 장바구니, 결제 후 고유 이용권 번호 생성 및 발급 |
| **게시판** | **고객센터** | **QnA(1:1문의), FAQ, 공지사항 게시판 및 다중 조건 검색** |
| **관리자** | 통계/관리 | **Chart.js**를 활용한 일간/주간 매출 및 연령별 이용객 시각화 |

<br>

## My Role (담당 업무)

**담당 역할: 게시판(Board) 기능 전반 및 통합 검색 구현**

### 1. 게시판(QnA/FAQ/Notice) CRUD 구현
- **MVC 패턴 & MyBatis 활용**: `Controller` - `Service` - `DAO(Mapper)` 구조로 역할을 분리하여 유지보수성 향상
- **QnA (1:1 문의)**: 회원이 문의 글 작성 시 관리자가 답글을 달 수 있는 계층형 구조 설계
- **템플릿 모듈화**: `jsp:include`를 활용하여 헤더, 푸터, 사이드바 등 반복되는 UI 요소를 모듈화

### 2. 다중 조건 검색 (Search)
- **동적 쿼리(Dynamic SQL)**: MyBatis의 `<if>`, `<choose>` 태그를 활용하여 제목, 내용, 작성자 등 **다양한 조건**으로 데이터를 필터링하는 검색 로직 구현
- **데이터 기반 추천**: 검색 데이터를 바탕으로 고객이 자주 찾는 정보를 FAQ 상단에 노출하는 로직 구상

<br>

## Trouble Shooting (성장 경험)

### 🚀 문제 상황: 이용권의 개별 식별 문제
사용자가 '입장권'을 2매 구매했을 때, `Order` 테이블에는 '수량: 2'로 저장되지만, 실제 입장 시에는 **두 장의 티켓을 각각 확인(사용 처리)** 해야 하는 문제가 있었습니다.

### 🔧 해결 과정
- 기존 `ino(상품코드)`만으로는 개별 티켓을 구분할 수 없음을 파악했습니다.
- **`issued_ticket` 테이블을 추가 설계**하여, 주문이 완료되면 구매 수량만큼 `Loop`를 돌며 고유한 `Ticket_ID`를 가진 레코드를 생성하도록 로직을 개선했습니다.
- 이를 통해 티켓별로 '사용 완료', '미사용', '만료' 상태를 각각 관리할 수 있게 되었습니다.

<br>
