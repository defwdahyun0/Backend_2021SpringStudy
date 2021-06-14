# Token, Local Storage, Paging

## Token

로그인 유지 방식에는 3가지가 있다.

- 쿠키, 세션
    - 자유이용권
        - 클라이언트가 로그인
        - 서버에서 쿠키, 세션 발급
            - (클라이언트가 회원용 api를 호출하면) http 헤더에 세션 id를 넣어서 자유롭게 사용할 수 있다.
            - 쿠키 : 클라이언트 스토리지
            - 세션 : 서버 스토리지
            - 세션 Id는 식별번호이다. abc1234 (서버 스토리지 이름)
    - 장점: 구현이 간단하다 (로그인 후 발급하면 계속 사용이 가능하다.)
    - 단점: 세션 ID가 노출 → 보안에 매우 취약하다.
- OAuth
    - Big-3 티켓 (한정적)
        - 특정 행위에 대해서만 티켓을 주는 것
            - 클라이언트가 로그인
            - 서버에서 토큰 발급
                - 발급해서 넘겨주기 전에, 퍼미션 리스트를 보낸다. 토큰마다 권한이 어떻게 다른지. 정보 권한 동의 체크가 퍼미션 리스트의 대표적인 예시이다
                - 바로 api에 접근 가능한 토큰을 주는 것이 아니라, 먼저 리프레시 토큰을 확인한다.
                - 엑세스 토큰을 발급 받은 후 api를 호출할 때 사용할 수 있다.
                    - 리프레시 토큰: 한 달 가량 유효
                    - 엑세스 토큰: 몇 분 단위로 갱신
                        - 리프레시 토큰을 발급받은 이후 엑세스 토큰을 발급
                        - 해킹 당할 기회를 줄인다.
                        - 리프레시 토큰이 중요한 권한이고, 엑세스 토큰이 부분적인 권한이다. 리프레시 토큰을 가지고 있으면서, 엑세스 토큰에는 중요하지 않은 정보를 보낸다. 리프레시 토큰의 출입을 최소화하한다.
        - 장점: 우수한 보안성
        - 단점: 과정이 복잡함
            - 리소스가 많이 필요
- JWT
    - 놀이기구를 탈 때마다 권한 검사
        - Json Web Token
        - {" ": " ", .. }
        - 클라이언트가 ID,PW 전송하면 서버가 ID,PW 정보로 64진법 인코딩 후 토큰으로 만들어서 전송한다.
            - 복잡하게 string으로 만든다.
            - 인코딩된 정보는 jwt의 페이로드에 해당된다.
        - jwt는 헤더, 페이로드, 시그니처와 같이 크게 3가지로 이루어져 있다.
            - 헤더: 메타데이터
            - 페이로드: ID, PW 인코딩된 정보
            - 시그니처: 헤더&페이로드를 암호화한 값
                - ID,PW를 검증해서 JWT가 유효한지 항상 체크를 해준다.
        - 장점: 리소스가 적게 필요하다.
        - 단점: 보안에 취약하다. → "HTTPS"
        - 발급시간, 유효기간, userIdx 등을 jwt 안에 넣는다.
        - [https://jwt.io/](https://jwt.io/)

## Local Storage

- 자동로그인
- 클라이언트가 어떻게 로그인을 유지시킬까?
- 로그인 토큰을 로컬 스토리지에 저장 → 자동로그인을 할 경우 저장된 로그인 토큰을 서버에 전송

## Paging

- 마지막 인덱스를 기억하고, 그 다음 인덱스를 페이징 크기에 맞추는 방식
    - ex) 게시글 리스트(1페이지, 2페이지...)
    - ex) 무한 스크롤 - 페이스북, 인스타
        - 홈화면 피드, 넘기면 다음 게시글이 계속해서 나온다.
        - 무한 스크롤 기능을 구현할 때 페이징 기술이 특히 중요하다.
- 페이지 카운터: 총 게시물 개수/한 페이지당 개수 = 페이지 수

## Transaction

- DB 상태를 변환시키는 하나의 논리적인 기능을 수행하기 위한 작업의 단위/ 한꺼번에 모두 수행되어야 할 일련의 연산들
- http method를 예시로 들면, put patch post delete (get x) 은 DB 상태를 변환시킬 수 있다.
- 계좌 입금 예시
    - 만약 입금을 하는데 과정 중간에 오류가 나면, 입금이 안됐는데 인출만 되는 상황이 발생한다.
    - 이러한 불상사를 예방하기 위해 처음으로 원상 복구를 해줘야 하는데, 이 과정이 transaction이다.
    - transaction이 진행되면 실행 전과 실행 후의 DB의 차이가 없어진다.

## Commit, Rollback

- Commit으로 프로젝트를 수행한다.
- Rollback을 통해 이전에 Commit했던 지점으로 돌아가서 상태를 원상 복귀 시킬 수 있다.