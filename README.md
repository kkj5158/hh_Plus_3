# STEP 5

# 시나리오 선정 및 마일스톤

## 시나리오 선정

### 콘서트 예약 서비스

### 선정 이유

대기열 시스템의 구축이라는 조건을 처음 마주했을때, 분산환경에서 유저에게 어떻게 대기열을 구현해줄 수 있을지 호기심이 생겼습니다. 또한 예약서비스 진행시에 시나리오에 기재된 다양한 조건속에서 동시성 이슈를 기술적으로 풀어보고 싶다는 생각이 들었습니다. 

![image](https://github.com/user-attachments/assets/99f5a634-2e29-4639-ba2b-e39c1b4df1ab)


## 마일스톤

- github issue, milestone , project를 통해서 작성함.

### 마일스톤

[Github 콘서트 예약 프로젝트 마일스톤 _ 김지승](https://github.com/kkj5158/hh_Plus_3/milestones)

### 프로젝트

[Github 콘서트 예약 프로젝트 _ 김지승](https://github.com/users/kkj5158/projects/2/views/3) 

![image 1](https://github.com/user-attachments/assets/4e5584ac-4c39-4df3-b025-cb030b703e7d)

# 요구 사항 분석 및 자료 제출

## 요구사항 분석

- 콘서트는 하루만 열리지 않고서 여러날 열림, 각각의 콘서트 날짜에 예약가능한 좌석의 수가 각기 다름
- 대기열 시스템의 필요
- 충전/사용 시스템의 필요
- 임시배정 정책이 있어야한다.
- 필요 API
    - 유저 토큰 발급 API
    - 예약 가능 날짜 / 좌석 API
    - 좌석 예약 요청 API
    - 잔액 충전 / 조회 API
    - 결제 API
- 분산환경을 고려해야 한다.
- 동시성 이슈의 고려가 필요하다

### 요구사항 분석에 따른 상세 정책 수립

첫번째로 , 요구사항 분석을 최우선으로 해서 정책을 수립하되, 차후에 충분히 추가될 가능성이 있는 정책들을 미리 고려해두어서 설계에 반영하고 싶었습니다. 첫 프로젝트 설계에서 이를 고려하지 않으면 확장성 있는 프로젝트 설계가 어렵다고 판단했습니다. 그래서 우선적으로 

1. 현재 요구사항 분석에 따른 상세 정책 
2. 해당 기능이 확장될 가능성을 고려한 상세 정책

을 모두 수립하였습니다. **2번을 미리 고려한 이유는 ERD 설계등 프로젝트 전체설계에서 확장가능성을 언제나 고려하면서 진행하고 싶었기 때문입니다.** 

#### 1. 현재 요구사항 분석에 따른 상세정책 

#### 유저 정책 

1. 콘서트를 예약하기 위해서 유저는 자신의 이름과 휴대폰 번호를 입력해야한다. 

##### 유저 포인트 정책 

1. 포인트 충전의 최소값은 1000원 , 최대값은 100만원이다. 
2. 보유 포인트의 최대값은 1000만원이다. 이를 넘는 포인트 충전은 불가능하다. 

#### 콘서트 정책 

1. 콘서트의 개최 일자는 매달 토,일 8번 가격은 10만원 , 좌석수는 50좌석으로 제한된다, 
2. 콘서트의 예약이 시작되는 날은 각 매달 말 일이다. 
    1.  마감이 되거나 이미 시작시간이 지난 콘서트는 예약이 불가능한 콘서트로 변경된다.
3. 유저는 콘서트 예약을 위해서는 미리 포인트를 충전해둬야한다. 
    1. 유저는 잔액을 언제든지 조회할 수 있다. 
4. 콘서트 예약가능 날짜는 유저의 편의를 위해서 남은 좌석수와 함께 제공되어야한다. ( 마감이 되었을 경우 그 즉시 마감처리되어서 유저의 불편을 최소화해야한다. ) 
    1. 마감의 기준은 임시배정을 포함해서 모든 좌석의 배정이 완료되었을 경우이다, ( 이는 입장열로 들어온 유저가 임시배정에 막혀서 좌석선택을 못하는 경우를 방지하기 위함이다.)
    2. 임시배정 → 예약가능 으로 변경되었을 경우 해당 날짜는 다시 예약가능날짜가 된다. 
        1. 그러므로 임시배정 → 예약가능 변경은 일종의 취소표와 같은 취급을 받는다. 

##### 대기열 시스템 정책  

1. 대기열 시스템은 ‘대기열’과 ‘입장열’로 나뉜다. 
    1. 입장열에 들어온 유저는 아무런 행동을 하지 않는 경우 자동적으로 입장열에서 제외된다. 유저에게 남은 시간을 제공해야한다.  ( 입장후 20분 ) 
2.  최대 대기열의 크기는 10000명이다. 
    1. 이때 최대 대기열의 크기를 넘는 유저가 대기열 토큰을 요청한 경우는 해당 유저에게 대기열도 꽉차있음을 알린다. 
    2. 입장열에 동시 입장 가능한 인원은 20명이다. 
    3. 입장열 입장 후 좌석 결제를 마친 인원은 퇴장시킨다, ( 좌석은 1인당 1개씩만 구매가능하다. ) 

#### 2. 해당 기능이 확장될 가능성을 고려한 상세 정책 

##### 유저 정책 

1. 콘서트를 예약하기 위해서 유저는 자신의 이름과 휴대폰 번호를 입력해야한다. 
2. 로그인 정책을 도입하여서 회원가입과 로그인에 성공한 유저만 로그인을 통해서 대기열에 진입할 수 있다. 
3. 유저는 자신의 예약 내역을 조회할 수 있다. 

##### 유저 포인트 정책 

1. 포인트 충전의 최소값은 1000원 , 최대값은 100만원이다. 
2. 보유 포인트의 최대값은 1000만원이다. 이를 넘는 포인트 충전은 불가능하다. 
3. 유저 포인트는 환불이 가능하다. 

##### 콘서트 정책 ( 확장 예상 시나리오 ) 

1. 콘서트의 개최 일자와 정보는 주최측이 등록한다. → 차후 자유롭게 등록하고 관리할 수 있는 기능이 필요할 수 있음 
    1. 개최일자, 좌석 등급별 가격 , 개최 장소를 정한다. 
2. 콘서트의 예약이 시작되는 날은 각 매달 말일부터  좌석이 마감이 되는 날 혹은 콘서트 시간 직전까지이다. 
    1.  마감이 되거나 이미 시작시간이 지난 콘서트는 예약이 불가능한 콘서트로 변경된다.
3. 매달 말일 매월 모든 콘서트의 예약이 가능하다. 콘서트의 예약가능좌석은 장소에 따라 달라진다, → 매달 말일이 아닌 날짜가 변경될 수 있음도 고려해야함 
    1. 예약콘서트장은 현재 다음의 3개의 장소이나, 언제든지 추가와 변경이 가능하다,
        1. 항해 콘서트장 - 50좌석 수용가능 
        2. 스파르타 콘서트장 - 100좌석 수용가능 
        3. 예술의 전당 콘서트장 - 1000좌석 수용가능 
4. 유저는 콘서트 예약을 위해서는 미리 포인트를 충전해둬야한다. 
    1. 유저는 잔액을 언제든지 조회할 수 있다. 
    2. 유저는 잔액을 환불받을 수 있어야한다. 
5. 콘서트의 가격은 모두 매 콘서트마다 주최가 정한값에 따른다. 
    1. 상세적으로는 날짜 , 좌석의 등급에 따라 달라진다. 
    2. 좌석의 등급은 A석, S석, R석, VIP석으로 나뉜다. 
    3. 그리고 이는 예약가능 날짜 콘서트, 남은 좌석수와 함께 정보로 제공되어야한다. 
6. 콘서트 예약가능 날짜는 유저의 편의를 위해서 실시간으로 남은 좌석수와 함께 제공되어야한다. ( 마감이 되었을 경우 그 즉시 마감처리되어서 유저의 불편을 최소화해야한다. ) 
    1. 마감의 기준은 임시배정을 포함해서 모든 좌석의 배정이 완료되었을 경우이다, ( 이는 입장열로 들어온 유저가 임시배정에 막혀서 좌석선택을 못하는 경우를 방지하기 위함이다.) 
        1. 임시배정 → 예약가능 으로 변경되었을 경우 해당 날짜는 다시 예약가능날짜가 된다. 
            1. 그러므로 임시배정 → 예약가능 변경은 일종의 취소표와 같은 취급을 받는다. 
    2. 임시배정 → 예약가능 변경은 임시배정 5분내로 결제가 이루어지지 않았을 경우로 한다. 
        1. 이때 결제가 이루어지지 않음은. 결제의 실패가 아닌. 결제의 미시도로 명확히 한다. 
            1. 결제정보를 잘못 입력해서 결제에 실패한 유저는 해당되지 않는다. 
            2. 정확하게 좌석 선택이후 결제정보를 입력하지 않거나, 입력했더라도 결제버튼을 누르지 않은 유저로 제한한다, 
            3. 유저에게 남은 결제시간을 명확히 표출해준다. 
            4. 결제 미시도 유저는 입장열에서 제외된다. 
            5. 결제실패 유저는 선택한 좌석에 한해서 재결제할 수 있는 기회가 주어진다. 
                1. 결제 실패의 사유는 다음과 같다 
                    1. 결제 정보의 오기재 
            6. 이때 5번이상 결제에 실패하면 올바르지 못한 접근으로 판단하고 유저를 입장열에서 제외하며 이를 안내한다. 
        2. 이때 해당 경우 예약가능 좌석으로 변경된다., 그리고 이 정보는 실시간으로 예약가능 좌석수에 반영된다. 
7. 유저는 예매한 콘서트 티켓을 콘서트 3일전까지 환불 가능하다. 

##### 대기열 시스템 정책  

1. 대기열 시스템은 ‘대기열’과 ‘입장열’로 나뉜다. 
2. 입장열에 들어온 유저는 좌석선택을 하지 않을경우 자동적으로 입장열에서 제외된다. 유저에게 남은 시간을 제공해야한다. 
3.  최대 대기열의 크기는 10000명이다. 
    1. 이때 최대 대기열의 크기를 넘는 유저가 대기열 토큰을 요청한 경우는 해당 유저에게 대기열도 꽉차있음을 알린다. 
    2. 입장열에 동시 입장 가능한 인원은 20명이다. 
    3. 입장열 입장 후 좌석 결제를 마친 인원은 퇴장시킨다, ( 좌석은 1인당 최대 4개씩 구매가능하다. ) ( 정책에 따라 변경가능하다 ) 

## 프로젝트 설계 문서화 작업

### 1. 플로우 차트

![image_(2)](https://github.com/user-attachments/assets/fefedf4c-0e14-4929-aba7-90743c2a2d6c)

### 2. 시퀸스 다이어그램

#### 1. 토큰 발급 및 폴링 시퀸스 다이어그램 

![image 2](https://github.com/user-attachments/assets/f179a7dd-4ac6-4502-bc10-62a261308ee5)


#### 토큰 usable 이후 예약 시퀸스 다이어그램 

![image 3](https://github.com/user-attachments/assets/a9db958b-6580-4d69-8664-59eea208f768)


#### 토큰 usable 이후 잔액 조회 , 충전 시퀸스 다이어그램 

![image 4](https://github.com/user-attachments/assets/5f96acd1-c91d-4b6b-8c4b-9f4fd6889daf)

## 요구사항 분석에 따른 기술 도입 및 상세 설계 ( 초안 )

### 사용기술 및 채택 이유 

#### Docker , Docker-compose 

#### Redis  

#### 

### 상세 설계 

![image 5](https://github.com/user-attachments/assets/092b6591-a607-45e6-8c5d-d32d033b93e5)


#### Redis의 Sorted Set을 활용한 Waiting 열과 Usable 데이터의 관리 

**Redis의 데이터 구조 중 Sorted Set은 Key, Score, Member의 형태로 이루어져 있으며 Score를 기반으로 정렬이 됩니다.**

- 최초 대기열 토큰요청시에 레디스 wait  데이터 조회 데이터가 10000명 초과면 대기열 진입실패 메시지를 반환
- 이하일 경우 대기열에 등록됨 대기열에  등록된 유저 아이디는 다음의 경우에 Usabl 로 이동
    - Usable의 데이터갯수가 20명이하인경우
- Usable의 데이터는 다음의 경우에 삭제 처리됨
    - 결제를 완료해서 좌석 예매가 완료된 경우
    - TTL 시간이 지나서 자동 삭제되는경우
- 스프링의 REDIS를 관리하는 잡 스케줄러는 Redis의 두가지열을 주기적으로 조회하여 . 대기열에 적재된 유저의 갯수와 입장열에 적재된 유저의 갯수를 주기적으로 관리하다.
    - 잡은 다음의 역할을 수행한다.
        - 특정 주기마다 실행된다. ( ex 1초)
        - Usable 데이터셋의 갯수를 센다 , 데이터가 20개 미만일 경우 Wait 데이터셋에서 가장 스코어가 높은 ( 가장 먼저 들어온 ) user_id를 TTL 20분으로 설정하는 
        새로운 데이터셋을 적재한다,
        - 이후 , Wait열에서 해당 데이터셋을 삭제한다.

#### 입장열 토큰검증 

API의 모든 요청은 토큰값 검증 후 수행된다.  토큰값 검증은 다음과 같이 이루어진다. 

- 모든 요청은 토큰의 UUID가 Usable에 적재되어있는지 조회하는 단계로 시작된다.
- 해당 UUID가 있을경우 API의 사용이 가능하다. 아닐경우 불가능하다.

#### 분산환경에서의 동시성 고려 

- Redis를 스케줄링에 따라서 Redis의 데이터 상태를 변경시키는 스케줄러는 하나의 인스턴스만 사용한다.

### 기술들에 대한 참고 문서들

[https://lsdiary.tistory.com/119](https://lsdiary.tistory.com/119)

[https://dev.gmarket.com/46](https://dev.gmarket.com/46)

[https://f-lab.kr/blog/redis-command-for-atomic-operation](https://f-lab.kr/blog/redis-command-for-atomic-operation)
