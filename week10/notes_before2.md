# week09/tools.py 함수 설명

`tools.py`는 영화진흥위원회(KOBIS) 오픈 API의 **일별 박스오피스** 데이터를 조회하는 도구 함수들을 담고 있다.

## 공통 설정

- `requests` 라이브러리로 HTTP 요청을 보낸다.
- API 키는 `secret.py`의 `API_KEY`에서 가져온다.
- `BASE_URL`: `https://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json`
- 두 함수 모두 `key`(API 키)와 `targetDt`(조회 날짜)를 파라미터로 보내고, 요청 제한 시간은 10초다.

## 함수 목록

### 1. `get_boxoffice_rank_1(date)`

해당 날짜의 박스오피스 **1위 영화 제목**을 돌려준다.

| 항목 | 내용 |
|------|------|
| 입력 | `date` (문자열) – 8자리 날짜, 예: `"20260101"` |
| 출력 | (문자열) 1위 영화 제목, 예: `"오디세이"` |

동작:
1. API에 해당 날짜의 일별 박스오피스를 요청한다.
2. 응답 JSON에서 `boxOfficeResult` → `dailyBoxOfficeList`를 꺼낸다.
3. 목록의 첫 번째 항목(`movies[0]`)의 `movieNm`(영화 제목)을 반환한다.

### 2. `get_movie_audience(date, title)`

해당 날짜에 특정 영화의 **일일 관객 수**를 돌려준다.

| 항목 | 내용 |
|------|------|
| 입력 | `date` (문자열) – 8자리 날짜, `title` (문자열) – 영화 제목 |
| 출력 | (문자열) 천 단위 쉼표가 붙은 관객 수, 예: `"123,456명"` |

동작:
1. API에 해당 날짜의 일별 박스오피스를 요청한다.
2. `dailyBoxOfficeList`를 돌면서 `movieNm`이 `title`과 정확히 같은 영화를 찾는다.
3. 찾으면 `audiCnt`(해당일 관객 수)를 정수로 바꿔 `"{관객수:,}명"` 형식으로 반환한다.
4. 못 찾으면 `"'{title}' 은(는) {date} 순위에 없습니다."`를 반환한다.

## 참고 사항

- `get_movie_audience`에는 docstring이 없다 (`get_boxoffice_rank_1`에만 있음).
- 두 함수가 요청·파싱 코드를 똑같이 반복하고 있다.
- 오류 처리가 없다: 네트워크 오류, 잘못된 API 키나 날짜, 빈 목록일 때 `get_boxoffice_rank_1`은 예외(`KeyError`, `IndexError` 등)가 날 수 있다.
- 영화 제목은 완전히 일치해야 찾는다 (띄어쓰기·부분 일치는 안 됨).
