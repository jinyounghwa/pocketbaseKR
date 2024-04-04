필터 규칙 표현식을 입력할 때 자동 완성 기능을 사용하여 안내받을 수 있지만, 일반적으로 다음 세 가지 필드 그룹에 액세스할 수 있습니다.

컬렉션 스키마 필드:
이는 모든 중첩 관계 필드도 포함합니다. 예: someRelField.status != "pending"

- @request.\_
  현재 요청 데이터에 액세스하는 데 사용됩니다. 예를 들어 쿼리 매개변수, 본문/양식 데이터, 인증된 사용자 상태 등입니다.
- @request.context - 규칙이 사용되는 컨텍스트(예: @request.context != "oauth2")
  현재 지원되는 컨텍스트 값은 default, oauth2, realtime, protectedFile입니다.
- @request.method - HTTP 요청 메서드(예: @request.method = "GET")
- @request.headers.\_ - 요청 헤더를 문자열 값으로 (예: @request.headers.x*token = "test")
  모든 헤더 키는 소문자로 표준화되고 "-"는 "*"로置き換え됩니다 (예를 들어 "X-Token"은 "x\*token"이 됩니다).
- @request.query.\* - 요청 쿼리 매개변수를 문자열 값으로 (예: @request.query.page = "1")
- @request.data.\_ - 제출된 본문 매개변수(예: @request.data.title != "")
- @request.auth.\_ - 현재 인증된 모델(예: @request.auth.id != "")
- @collection.\*
  이 필터는 현재 컬렉션과 직접 관련이 없지만 (즉, 이를 가리키는 관계 필드가 없음) 둘 다 공통 필드 값(예: 카테고리 ID)을 공유하는 다른 컬렉션을 대상으로 사용할 수 있습니다.
  `@collection.news.categoryId ?= categoryId && @collection.news.author ?= @request.auth.id`
  동일한 컬렉션을 여러 번 조인하지만 다른 조건을 기반으로 할 경우 컬렉션 이름에 :alias 접미사를 붙여 별칭을 정의할 수 있습니다. // 참고: https://github.com/pocketbase/pocketbase/discussions/3805#discussioncomment-7634791

`@request.auth.id != "" && @collection.courseRegistrations.user ?= id && @collection.courseRegistrations:auth.user ?= @request.auth.id && @collection.courseRegistrations.courseGroup ?= @collection.courseRegistrations:auth.courseGroup`

구문은 일반적으로 다음 형식을 따릅니다.

OPERAND - 위의 필드 리터럴, 문자열(글 또는 더블 따옴표), 숫자, null, true, false 중 하나일 수 있습니다.
OPERATOR - 다음 중 하나입니다.
= 동일
!= 같지 않음

- 크다
- = 크거나 같음
- < 작다
- <= 작거나 같음
- ~ 포함 (지정되지 않은 경우 오른쪽 문자열 피연산자를 와일드카드 일치를 위해 "%"로 자동 래핑)
- !~ 포함하지 않음 (지정되지 않은 경우 오른쪽 문자열 피연산자를 와일드카드 일치를 위해 "%"로 자동 래핑)
- ?= / 적어도 하나의 동일
- ?!= / 적어도 하나의 같지 않음
- ?> / 적어도 하나의 크다
- ?>= / 적어도 하나의 크거나 같음
- ?< / 적어도 하나의 작다
- ?<= / 적어도 하나의 작거나 같음
- ?~ / 적어도 하나의 포함 (지정되지 않은 경우 오른쪽 문자열 피연산자)
