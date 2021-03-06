## HTTP 완벽가이드

### :one::six: 장 국제화

이 장의 궁극적인 목표는 **인코딩** 과 **언어 태그** 를 제대로 알고 다루기 위한 것이 목적임다. 

#### 국제적인 콘텐츠를 다루기 위한 헤더

각 나라별로 HTTP 엔티티에 담겨지는 내용이 다르므로, 이에 대한 공통 포맷 헤더가 필요하다. 

그래서 클라이언트 :man: 는 서버에게 `내가 어떠한 언어를 해독할 수 있는지에 대해` 명시하는 헤더가 필요한데. 

이것이 바로.!

```http
Accept-Language: fr, en;q=0.8 // q는 quality factor의 약자로, 기본은 1.0이며 영어에만 0.8 우선순위를 두었다는 내용
Accept-Charset: iso-8859-1, utf-8
```

#### 문자집합과 HTTP

그래서 어떻게 나라별로 언어를 쉽게 지원할 수 있는가?  

우리가 쓰는 흔히 `Charset` 은 **글자를 비트로 변환하는 인코딩** 이다. 

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=iso-8859-6
Content-Length: 1857
Content-Language: ar
```

위 메세지 포맷은 아랍어로된 엔티티를 `iso-8859-6` 인코딩 구조로 변환되었다고 알려주는 헤더들이다!  

#### 문자집합과 인코딩은 어떻게 동작하는가?

<div>
  <img width="452" alt="encoding" src="https://user-images.githubusercontent.com/34855745/95654319-434c1380-0b3a-11eb-8f4b-a3108da5099f.png">
</div>


크게 2단계로 이루어지는데,

1. 문서를 이루는 비트들은, 특정 코딩된 문자집합의 특정문자로 식별될 수 있는 문자코드로 변환된다
2. 문자 코드는 코딩된 문자집합의 특정 요소를 선택하기 위해 사용된다

그 다음 프레젠테이션 로직을 통해서 글자 모양이 탄생하게 되는거죠.!  

그래서 표준화된 MIME charset :abc: 은 엄청 많으니까 관심 있으시면 찾아보세요 ㅎㅎ

```text
us-ascii, iso-8859-1,2,5,6,7 ..., euc-jp, utf-8, windows-1252, euc-kr
```

보통 흔히 쓰이는 딱 4가지만 짚고 넘어가겠습니다.!

* `UTF-8` : 전 세계의 보편적 문자집합인 UCS(유니코드)를 표현하기 위한 흔히 쓰이는 가변길이 문자 인코딩 구조 방식
  * 그래서 각 글자를 1~6 Byte 로 표현하는 가변길이 인코딩을 문자 코드 값에 사용한다
* `US-ASCII` : 미국에서 정보교환을 위한 표준으로 사용되었으며, 우리가 흔히 아는 그 아스키코드
* `iso-6659-6` : `iso-8859` 문자집합 표준은 국제적인 글쓰기를 위해 필요한 글자들을 하이 비트로 이용해서 추가한 `US-ASCII`의 8비트 확장방식
  * 아랍 문자를 표현하기 위해 `ASCII` 를 확장한 방식
* `euc-kr` : 한글 인터넷 문서를 위해 널리 사용되는 가변길이 인코딩 방식으로, `KS X 1003`과 `KS X 1001` 2가지 문자집합을 사용한다.!

#### 헤더를 사용하는 방식

`Content-Type` 헤더에 `charset` 으로 표기하는 방식을 통해, 클라이언트 혹은 서버가 HTTP 엔티티를 명시하거나,  

클라이언트는 서버가 응답해주기를 바라는 `Accept-Charset` 을 통해서 한정된 인코딩 구조로 응답하라고 요청할 수도 있다. 

```HTTP
Accept-Charset: iso-8859-6, utf-8
```

그러면 서버는 인코딩 방식을 2개중 하나를 택해서 응답하게 될 것이다. 

### 다중언어 문자 인코딩에 대한 지침

이 챕터는 용어들이 되게 많다

* 문자: 우리가 아는 그 문자 맞는데, 모든 문자를 의미한다
* 글리프: 하나의 글자를 표현하기 위한 획의 패턴이나 다른 것과 구분되는 유일한 시각적 형태
* 코딩된 문자: 우리가 각 글자를 다룰 수 있도록 각 글자에 할당된 유일한 숫자
* 코드 공간: 문자 코드 값으로 사용하려고 계획해 둔 정수의 범위
* 코드 너비: 각 문자 코드의 비트 개수
* 사용가능한 문자집합: 글자들에 대한 특정한 작업 집합
* 코딩된 문자집합: 사용 가능한 문자집합을 받아서 **각 글자에 코드 공간의 코드를 할당해주는 코딩된 문자들의 집합**
* 문자 인코딩 구조: 숫자로 된 문자 코드들을 콘텐츠 비트의 연속으로 인코딩하는 알고리즘

중요한 몇가지만 짚고 넘어가면~. 

<div>
  <img width="449" alt="coded-charset" src="https://user-images.githubusercontent.com/34855745/95654323-49da8b00-0b3a-11eb-9290-fea0d59aeb4d.png">
</div>


### 언어 태그와 HTTP

위에서 언급한 `Content-Language` 헤더로 어떤 언어를 나타낼 것인지 명시하는 헤더다. 

마찬가지로 클라이언트는 서버가 응답해주길 바라는 언어를 요청할 떄는 `Accept-Language` 헤더로 명시하면 된다!  

그래서 언어 태그들은 3가지의 태그로 나뉘어지며,

```text
sgn-US-MA 이런 언어 태그 헤더가 있다면
(첫 번째 언어태그(수화)) - 두 번쨰 서브태그 - 세번째 서브태그
```

이런식으로 서브 태그들을 명시할 수 있다. 

1. 첫 번째 언어태그는 `ISO-639` 언어 표준에서 정의된 이름 공간을 의미하고
2. 두 번째 언어태그는 `ISO-3166` 에서 정의된 국가 코드를 의미하고
3. 나머지 태그는 약간 방언을 위한 태그들이다

그래서 가장 많이쓰이는 건 **첫 번째 언어태그** 이며 예시는 아래와 같다. 

```text
ko, en, fr, ja, ... (한국어, 영어, 프랑스어, 일본어...)
```

궁금하면 더 찾아보자 ㅎㅎ :smile:  

### 국제화된 URI

요즘 같은 시대에는 잘 쓰이지 않는다고 하더라..  

이 책에있는 내용을 요약하면, 

**URI 이스케이프는 예약된 문자나 다른 지원하지 않는 글자를 안전하게 URI에 삽입할 수 있는 방법을 제공하는 방식이다.**. 

 그러니까 이스케이프를 위해서는 `%<HEX><HEX>` 로 인코딩해서 URI에 꼭 명시하라는 내용이다.!

<div>
  <img width="456" alt="escape" src="https://user-images.githubusercontent.com/34855745/95654321-47783100-0b3a-11eb-8f67-32b63bd0d940.png">
</div>


### 기타 고려사항

도메인 이름의 경우에는 `퓨니코드(punycode)` 를 이용해서 지원하는 경우가 많다. 

> 퓨니코드는 유니코드 문자열을 호스트 명에서 사용가능한 문자만으로 이루어진 문자열로 변환하는 방법이다

그래서 이 기법을 이용하면 **다국어로 된 도메인 이름을 알파벳고 숫자 등으로 된 도메인으로 바뀌게 된다!**

예를 들면, `한글.com` 은 `xn--bj0bj06e.com` 으로 변환된다는 의미다!

