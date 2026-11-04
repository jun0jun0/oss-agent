# week09 `tools.py` 함수 정리 (수정 전)

`week09/tools.py`는 영화진흥위원회(KOBIS) 오픈 API의 **일별 박스오피스** 엔드포인트를 호출하는 함수 2개를 담고 있다.

## 공통 사항

- API 주소: `https://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json`
- API 키는 `secret.py`의 `API_KEY`에서 가져온다.
- 요청 파라미터: `key`(API 키), `targetDt`(조회 날짜, `YYYYMMDD` 8자리 문자열)
- `requests.get(..., timeout=10)`으로 호출하고, 응답 JSON의 `boxOfficeResult.dailyBoxOfficeList`에서 영화 목록을 꺼낸다.

## 1. `get_boxoffice_rank_1(date)`

- **역할**: 주어진 날짜의 박스오피스 1위 영화 제목을 반환한다.
- **입력**: `date` (문자열) – 8자리 날짜, 예: `"20260101"`
- **출력**: (문자열) 1위 영화 제목, 예: `"오디세이"`
- **동작**: 영화 목록의 첫 번째 항목(`movies[0]`)의 `movieNm` 값을 돌려준다.
- **docstring**: 있음 (역할·입력·출력이 한국어로 설명되어 있음)

## 2. `get_movie_audience(date, title)`

- **역할**: 주어진 날짜에 특정 영화의 일일 관객 수를 반환한다.
- **입력**:
  - `date` (문자열) – 8자리 날짜
  - `title` (문자열) – 영화 제목 (목록의 `movieNm`과 정확히 일치해야 함)
- **출력**: (문자열)
  - 찾은 경우: 천 단위 쉼표가 붙은 관객 수, 예: `"123,456명"`
  - 못 찾은 경우: `"'{title}' 은(는) {date} 순위에 없습니다."`
- **동작**: 영화 목록을 순회하며 제목이 일치하는 항목의 `audiCnt`를 정수로 바꿔 포맷한다.
- **docstring**: **없음**

## 눈에 띄는 점

- 두 함수가 API 호출·JSON 파싱 코드를 똑같이 반복한다.
- `get_movie_audience`에는 docstring이 없어, 에이전트(LLM)가 도구 설명을 얻기 어렵다.
- 오류 처리가 없다: 네트워크 오류, 잘못된 날짜, 빈 목록일 때 `movies[0]`에서 `IndexError` 등이 날 수 있다.
- 제목 비교가 완전 일치라서 띄어쓰기나 부분 제목은 찾지 못한다.
