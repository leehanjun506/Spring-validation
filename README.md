# Summary
## 컨트롤러의 중요한 역할중 하나는 HTTP 요청이 정상인지 검증하는 것이다.    

### 클라이언트 검증은 조작할 수 있으므로 보안에 취약하다. 서버만으로 검증하면,즉각적인 고객 사용성이 부족해진다. 둘을 적절히 섞어서 사용하되, 최종적으로 서버 검증은 필수 API 방식을 사용하면 API 스펙을 잘 정의해서 검증 오류를 API 응답 결과에 잘 남겨주어야 함
</br>

### BindingResult 가 있으면 @ModelAttribute 에 데이터 바인딩 시 오류가 발생해도 컨트롤러가 호출된다!
### ex) @ModelAttribute에 바인딩 시 타입 오류가 발생하면?
### BindingResult 가 없으면 400 오류가 발생하면서 컨트롤러가 호출되지 않고, 오류 페이지로 이동한다.
### BindingResult 가 있으면 오류 정보( FieldError )를 BindingResult 에 담아서 컨트롤러를 정상 호출한다

------------------
## Spring Bean Validation : 검증 로직을 공통화하고, 표준화 한것
### 이를 사용하면 애노테이션 하나로 검증 로직을 편리하게 적용 가능
### 검증 애노테이션
### @NotBlank : 빈값+공백만 있는 경우 허용 x
### @NotNull : null 허용하지 않는다
### @Range(min = 1000, max = 1000000) : 범위 안의 값이어야 한다.
### @Max(9999) : 최대 9999 까지만 허용한다.
</br>

### 스프링 부트가 spring-boot-starter-validation 라이브러리를 넣으면 자동으로 Bean Validator를 인지하고 스프링에 통합한다.
### 글로벌 Validator가 적용되어 있기 때문에, @Valid or @Validater만 적용하면 된다.
### 검증 오류가 발생하면 FieldError', 'ObjectError'를생성해서'BindingResult'에 담아준다.
</br>

### 검증 순서
1. @ModelAttribute 각각의 필드에 타입 변환 시도
2. 성공하면 다음으로
3. 실패하면 typeMismatch 로 FieldError 추가
4. Validator 적용

### 바인딩에 성공한 필드만 Bean Validation 적용
### BeanValidator는 바인딩에 실패한 필드는 BeanValidation을 적용하지 않는다.
### 생각해보면 타입 변환에 성공해서 바인딩에 성공한 필드여야 BeanValidation 적용이 의미 있다. (일단 모델 객체에 바인딩 받는 값이 정상으로 들어와야 검증도 의미가 있다.)

### Bean Validation에서 ObjectError 처리 -> @ScriptAssert() 사용
### 하지만 제약이 많기 때문에 ObjectError 관련 부분은 직접 작성하는 것 권장한다.
</br>

### Bean Validation 한계
### 동일한 모델 객체를 가지고 등록과 수정등의 행위를 할때 검증 조건의 충돌이 발생할 수 있어 같은 BeanValidation을 적용할 수 없다.
</br>

---

## 동일한 모델 객체를 등록, 수정 할때 각각 다르게 검증하는 방법
1. BeanValidation의 groups 기능 사용
2. 별도의 모델 객체를 만들어서 사용

---
## Bean Validation - HTTP 메시지 컨버터
### @ModelAttribute 는 HTTP 요청 파라미터(URL 쿼리 스트링, POST Form)를 다룰 때 사용한다.
### @RequestBody 는 HTTP Body의 데이터를 객체로 변환할 때 사용한다. 주로 API JSON 요청을 다룰 때 사용한다
</br>

### API의 경우 3가지 경우를 나누어 생각해야 한다.
1. 성공 요청: 성공
2. 실패 요청: JSON을 객체로 생성하는 것 자체가 실패함
3. 검증 오류 요청: JSON을 객체로 생성하는 것은 성공했고, 검증에서 실패함
