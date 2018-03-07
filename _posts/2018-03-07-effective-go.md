---
layout: post
title: effective go 정리
tags: [go]
---
### 포맷팅

`gofmt` 혹은 `go fmt` 프로그램은 표준 스타일의 들여쓰기와 수직 정렬, 유지 그리고 필요시 주석을 재포맷팅한 소스코드를 내놓는다.

```go
type T struct {
    name string // name of the object
    value int // its value
}
```

`gofmt`는 다음과 같이 정렬한다.
```go
type T struct {
    name    string // name of the object
    value   int    // its value
}
```

### 주석

모든 패키지는 블럭 주석 형태의 패키지 주석이 있어야 한다. 패키지 주석은 패키지를 소개해야 한다. 패키지 주석은 godoc 문서의 처음에 나타나게 되니 이후의 자세한 사항도 작성해야 한다.

주석은 해석되지 않는 일반 텍스트이다. 그래서 HTML이나 `_this_` 같은 주석은 작성된 그대로 나타나므로 사용하지 않는 것이 좋다.

패키지에서 최상위 선언 바로 앞에 있는 주석이 그 선언의 문서 주석으로 처리된다. 외부로 노출되는(대문자로 시작되는) 모든 이름은 문서 주석이 필요하다.

```go
// Compile parses a regular expression and returns, if successful,
// a Regexp that can be used to match against text.
func Compile(str string) (*Regexp, error) {
```

> 주석의 첫 문장은 선언된 이름으로 시작하는 한 줄 짜리 문장으로 요약해야한다.

만약 그 이름이 항상 주석으로 시작된다면, godoc의 결과는 grep 문을 이용하는 데 유용하다.

```
$ godoc regexp | grep parse
```

### 변수 선언의 그룹화

그룹화는 항목 간의 관련성을 나타낸다. 예를 들어 다음 변수의 그룹은 mutex에 의해 보호되고 있음을 나타낸다.

```go
var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```

### 명칭
