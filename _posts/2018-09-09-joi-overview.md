---
layout: post
title: Joi, Javascript object schema validator
---

## 시작하기

API 수준이든, 함수 수준이든 외부 입력을 항상 validation하는 습관은 중요하다. 귀찮다고 빼 먹는다면 언젠가는 분명 문제를 일으킬 것이다. 많은 node용 validator가 있지만 `Joi`가 우수한 점은 **관계**에 따른 내용을 정의할 수 있다는 점이다. 예를 들어 `a`와 `b`가 둘 중에 하나라도 있으면 된다든가, `a`가 있을 때만 `b`가 있어야 된다는 식이다.

여기서 다루는 `Joi`의 모든 기능은 [`API 13.6.0`](https://github.com/hapijs/joi/blob/v13.6.0/API.md)을 따르고 있다.

**관계**에 따른 `Joi` 사용법은 뒤에서 알아보고 우선은 `Joi`의 간단한 사용법부터 알아보자.

```js
const Joi = require('joi');

// validate할 schema 정의
const schema = Joi.object().keys({
  username: Joi.string().min(3).max(30).required(),
  birthyear: Joi.number().integer().min(1900).max(2018),
});

// validate할 object
const user = {
  username: 'username1',
};

// validate
const {error, value} = Joi.validate(user, schema);
console.log(error); // null
console.log(value); // {username: "username1"}
```

입력인 `user`가 미리 정의된 `schema`와 일치하는지를 확인한다.

`schema`는 다음과 같이 정의되었다.

- `username`: string이고, 길이가 최소 3글자에서 최대 30글자이며, 필수 항목임.
- `birthyear`: number이고, integer이며, 최소 1900에서 최대 2013까지 허용하고, 옵션 항목임.

따라서 `user.username`은 `schema`에 정의된 내용에 부합하고, `user.birthyear`는 optional로 정의되었기 때문에 없어도 문제 없이 validate되었다.

에러가 나는 경우는 어떻게 되는지 알아보자.

```js
const Joi = require('joi');

const schema = Joi.object().keys({
  username: Joi.string().min(3).max(30).required(),
  birthyear: Joi.number().integer().min(1900).max(2018),
});

const user = {
  username: 'username1',
  birthyear: 2020,
};
const {error, value} = Joi.validate(user, schema);
console.log(error); // Error: child "birthyear" fails because ["birthyear" must be less than or equal to 2018]
console.log(value); // {username: "username1", birthyear: 2020}
```

`user.birthyear`가 정의된 최대값 2018보다 크기 때문에 validate error가 났다. `Joi.validate` return 값의 `value`는 원래 입력값인 `user`의 내용이 그대로 표시되므로, return 값의 `error`가 null 인지 아닌지로 validate 성공, 실패 여부를 확인하면 된다.

## Joi의 동작

기본적인 중요한 부분은 훑었으니, `Joi`의 동작 방식을 몇 가지 알아보자.

### .unknown() API

`schema`에 정의되지 않은 key는 validate error가 난다. 이를 피하려면 [`.unknown()`](https://github.com/hapijs/joi/blob/v13.7.0/API.md#objectunknownallow) API를 쓰면 된다.

```js
const Joi = require('joi');

// .unknown()이 없는 경우
const schema1 = Joi.object().keys({
  username: Joi.string().min(3).max(30).required(),
});

const user1 = {
  username: 'username1',
  birthyear: 2014,   // schema1에 정의되지 않은(unknown) 'birthyear'
};
const {error: error1, value: value1} = Joi.validate(user1, schema1);
console.log(error1); // Error: "birthyear" is not allowed
console.log(value1); // {username: "username1", birthyear: 2014}


// .unknown()이 있는 경우
const schema2 = Joi.object().keys({
  username: Joi.string().min(3).max(30).required(),
})
  .unknown();  // 정의되지 않은 key도 허용함.

const user2 = {
  username: 'username1',
  birthyear: 2014,   // schema1에 정의되지 않은(unknown) 'birthyear'
};
const {error: error2, value: value2} = Joi.validate(user2, schema2);
console.log(error2); // null
console.log(value2); // {username: "username1", birthyear: 2014}
```

### abortEalry option

정의된 key 중 에러가 나면 더 이상 진행하지 않는 것이 기본 동작(`abortEarly: true`)이다. 한 번에 모든 에러를 확인하고 싶으면 validate 시점에 동작을 제어할 수 있는 [`validate()`의 세 번째 파라미터](https://github.com/hapijs/joi/blob/v13.7.0/API.md#validatevalue-schema-options-callback)로 `{abortEarly: false}`를 설정하면 된다.

```js
const Joi = require('joi');

const schema = Joi.object().keys({
  username: Joi.string().min(3).max(30).required(),
  birthyear: Joi.number().integer().min(1900).max(2018),
});

const user = {
  birthyear: 2020,
};

const options: {
  abortEarly: false,
};

const {error, value} = Joi.validate(user, schema, options);
console.log(error); // Error: child "username" fails because ["username" is required]. child "birthyear" fails because ["birthyear" must be less than or equal to 2018]
console.log(value); // {birthyear: 2020}
```

`.required()`인 `username`이 없는 error와 `.max(2018)`을 위반한 `birthyear`가 모두 validate error로 검출되었다.

이 외에도 여러가지 option이나 API가 있으니, 상황에 맞게 사용하면 된다.


## 관계 API

끝으로, 관계에 관련된 API를 알아보자.

`.and`, `.or`, `.nand`, `.xor`는  bit 연산과 동일하게 생각하면 된다. 예를 들어 `.or`는 정의된 key가 하나 이상이나 모두 있으면 되지만, `.xor`는 정의된 key가 모두 있는 경우에 validate error가 난다.

```js
const Joi = require('joi');

const schema = Joi.object().keys({
  handphone: Joi.string(),
  homephone: Joi.string(),
})
  .xor('handphone', 'homephone')
  ;

const user = {
  handphone: '01011112222',
  homephone: '023334444',
};
const {error, value} = Joi.validate(user, schema);
console.log(error); // Error: "value" contains a conflict between exclusive peers [handphone, homephone]
console.log(value); // {handphone: "01011112222", homephone: "023334444"}
```

`handphone`과 `homephone`을 `.and`로 묶었기 때문에 `homephone`이 없어서 validate error가 발생했다.

`.with`, `.without`은 기준 key에 대해서 있어야 되거나 없어야 되는 관계를 설정한다.

```js
const Joi = require('joi');

const schema = Joi.object().keys({
  address1: Joi.string(),
  address2: Joi.string(),
})
  .with('address1', 'address2')
  ;

const user = {
  address1: 'address1',
};
const {error, value} = Joi.validate(user, schema);
console.log(error); // Error: "address1" missing required peer "address2"
console.log(value); // {address1: "address1"}
```

`address1`가 있는데 `address2`가 없는 경우에만 validate error가 난다.

끝으로 key 관계에 조건을 걸 수 있는 `.when` 예제를 보자.

```js
const Joi = require('joi');

const schema = Joi.object().keys({
  handphone: Joi.string(),
  homephone: Joi.string(),
  mobile_os: Joi.string().when('handphone', {
    is: Joi.exist(),
    then: Joi.string().required(),
    otherwise: Joi.forbidden(),
  }),
})
  ;
```

위 schema를 말로 표현하면 `mobile_os`는 `handphone`이 있는 경우에만 유효하고, `handphone`이 없는데 `mobile_os`가 있다면 validate error가 난다.

```js
// handphone과 mobile_os가 모두 있어 validate success
const user1 = {
  handphone: '01011112222',
  mobile_os: 'ios',
};
const {error: error1, value: value1} = Joi.validate(user1, schema);
console.log(error1); // null
console.log(value1); // {handphone: "01011112222", mobile_os: "ios"}

// handphone이 없는데  mobile_os가 있어 validate error
const user2 = {
  homephone: '023334444',
  mobile_os: 'ios',
};
const {error: error2, value: value2} = Joi.validate(user2, schema);
console.log(error2); // Error: child "mobile_os" fails because ["mobile_os" is not allowed]
console.log(value2); // {homephone: "023334444", mobile_os: "ios"}
```
