# 모듈

모듈이란?

- 애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각
- 일반적으로 기능을 기준으로 파일 단위로 분리
- 파일 스코프(모듈 스코프)를 가질 수 있어야 함

모듈은 애플리케이션과 분리되어 개별적으로 존재하다가 다른 모듈에 의해 재사용된다. 즉 코드의 단위를 명확히 분리할 수 있어 개발 효율성과 유지보수성을 높인다.

export: 모듈의 공개가 필요한 자산을 명시적으로 선택적 공개하는 것

import: 공개된 모듈의 자산의 일부 또는 전체를 모듈 스코프 내로 불러들여 재사용하는 것

## 자바스크립트와 모듈

C, 자바 등 대부분의 프로그래밍 언어와 달리 자바스크립트는 모듈 시스템을 지원하지 않았었고, 전역 변수가 중복되는 등 문제가 발생하였다.

그 후 자바스크립트를 브라우저 환경 외에서 범용적으로 사용하려는 움직임이 생기고 모듈 시스템을 구현하기 위해 CommonJS와 AMD(Asynchronous Module Definition)가 제안되었다.

자바스크립트 런타임 환경인 Node.js는 사실상 표준인 CommonJS를 채택했고, 그 후 독자적으로 진화해 기본적으로는 CommonJS 사양을 따르지만 완전히 동일하지는 않다.

즉 Node.js는 ECMAScript 표준 사양은 아니지만 모듈 시스템을 지원하기에 Node.js 환경에서는 파일별로 독립적인 모듈 스코프를 가진다.

## ES6 모듈 (ESM)

ES6에서는 클라이언트 사이드에서도 동작하는 모듈 기능이 추가되었으며 IE를 제외한 대부분의 브라우저에서 사용할 수 있다.

script 태그에 `type='module'` 속성을 추가하면 해당 자바스크립트 파일은 모듈로서 동작한다. ESM임을 명확히 나타내기 위해 파일 확장자는 .mjs를 사용할 것이 권장된다.

또한 ESM에는 기본적으로 strict mode가 적용된다.

```jsx
<script type="module" src="module.mjs"></script>
```

### 모듈 스코프

일반적인 자바스크립트 파일은 script 태그로 분리해 로드해도 독자적인 파일 스코프를 갖지 않기 때문에 분리된 script태그로 로드된 2개의 자바스크립트 파일은 하나의 자바스크립트 파일 내에 있는 것처럼 동작한다.

따라서 하나의 자바스크립트 파일에서 선언한 변수는 다른 파일에서도 참조가 가능한데, 이런 경우 변수가 의도치 않게 중복 선언되거나 변경되는 등 문제가 발생할 수 있다.

ESM은 파일 자체의 독자적인 파일 스코프(모듈 스코프)를 제공하기 때문에 이런 문제가 발생하지 않는다.

따라서 모듈 내에서 var 키워드로 선언한 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다. 또한 모듈 내에서 선언한 식별자는 모듈 외부에서 참조할 수 없다.

```jsx
// module1.mjs
var x = "hello";
var y = "hello2";
console.log(x); // hello
console.log(window.x); // undefined
```

```jsx
// module2.mjs
var x = "bye";
console.log(x); // bye
console.log(y); // ReferenceError: y is not defined
```

### export 키워드

모듈 내부에서 선언한 식별자는 기본적으로 해당 모듈의 내부에서만 참조할 수 있지만, export 키워드를 사용하면 외부에 공개하여 다른 모듈에서 재사용할 수 있다.

export 키워드는 선언문 앞에 사용한다. 변수, 함수, 클래스 등 모든 식별자를 내보낼 수 있다.

```jsx
export const test = "test";

export function test2() {
  return true;
}
```

export할 대상을 하나의 객체로 구성하여 한번에 export할 수도 있다.

```jsx
const test = "test";

function test2() {
  return true;
}

export { test, test2 };
```

### import 키워드

다른 모듈에서 export한 식별자를 모듈 스코프 내부로 로드하려면 import 키워드를 사용하면 된다. 이 때 다른 모듈이 export한 식별자 이름으로 import해야 한다.

또한 ESM의 경우 파일 확장자를 생략할 수 없다.

```jsx
import { test, test2 } from "./test.mjs";

console.log(test); // test
```

식별자 이름을 일일히 지정하지 않고 하나의 이름으로 한번에 import하거나, 식별자 이름을 변경하여 import할 수도 있다.

```jsx
import * as lib from "./test.mjs";

console.log(lib.test); // test
```

```jsx
import { test as T } from "./test.mjs";

console.log(T); // test
```

모듈에서 하나의 값만 export할 때는 default 키워드를 사용할 수 있고, 이 때는 이름 없이 하나의 값을 export하게 된다. default 키워드를 사용할 때는 var, let, const 키워드를 사용할 수 없다.

default 키워드로 export한 모듈은 {} 없이 임의의 이름으로 import하면 된다.

```jsx
// default.mjs
export default (num) => num + 1;
```

```jsx
// app.mjs
import sample from "./default.mjs";

console.log(sample(5)); // 6
```
