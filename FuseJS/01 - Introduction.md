<documentProperties pageTitle="Learn FuseJS" />

# 소개

$(FuseJS)는 크로스-플랫폼 모바일 앱의 비즈니스 로직을 작성하기 위한 $(JavaScript) 프레임워크 입니다.

노트: FuseJS는 현재 [WebGL 퍼블리시를 지원하지 않습니다](/learn/guides/previewandexport/other).

## 시작하기

FuseJS는 `<JavaScript>` 태그를 통해 @(UX Markup)에서 외부 자바스크립트 파일을 이용할 수 있습니다:

	<JavaScript File="SomeCode.js" />

인라인으로 작성할 수도 있습니다:

	<JavaScript>
		console.log("Hello, FuseJS!");
	</JavaScript>

## 모듈

FuseJS는 <a href="http://www.commonjs.org/">CommonJS</a> 모듈 시스템을 구현했습니다. 각 코드 파일 또는 인라인 스니펫은 _module_ 입니다.

모듈 안에 있는 것들은 외부에서 참조 가능합니다. 우리는 `module.exports`를 사용합니다:

	<JavaScript>
		module.exports = {
			exportedSymbol: "Hello, rest of the world!"
		};
	</JavaScript>

모듈내에 정의하지 않으면 외부에서 참조할 수 없습니다:

	<JavaScript>
		var data = [1, 2, 3];
		var invisible = "I'm invisible";

		module.exports = {
			data: data
		};
	</JavaScript>

이것은 private한 변수로 쓰기에 좋습니다.

> ## 모듈 가져오기

각 코드 파일(또는 인라인 스니펫)은 모듈입니다.

`$(require)`를 통해 다른 모듈에서 사용할 수 있는 모듈을 만들려면, `<JavaScript>` 태그의 속성에 `ux:Global` 을 추가하세요.

	<JavaScript File="someJavaScriptFile.js" ux:Global="MyModule" />

이제 다른 모듈에서 가져다 쓸 수 있습니다:

	var myModule = require('MyModule');

> 노트: 위 방법으로 할 경우 직접적으로 파일 경로를 써서 모듈을 가져올 수는 없습니다.

> ## 파일 경로로 가져오기

파일 경로로 모듈을 가져오는 방법입니다. .unoproj에 아래와 같이 추가하세요. JS 파일명 끝에 ':Buldle'을 주의하세요.

```
"Includes": [
	"yourJavaScriptFile.js:Bundle"
	..other files ..
]
```

또는 모든 JS 파일을 가져올 수 있습니다:

```
"Includes": [
	"**.js:Bundle"
]
```

이제 모듈을 이용할 수 있습니다:

```
var myModule = require('/someJavaScriptFile.js');
```

노트. '/'는 루트 디렉토리를 의미합니다. 현재 디렉토리를 참조하려면 './'를 이용하세요.

```
var relativeToProjectRoot = require('/SomeComponent.js');
var relativeFile = require('./MainView.js');
var relativeToRootOrGlobalModule = require('SomeOtherComponent.js');
```

* 노트. 원한다면 .js를 생략할 수 있습니다.


예제:

* <a href="https://www.fusetools.com/developers/examples/todoparseexample">Parse를 이용하는 TODO 앱</a>

## Design and motivation

The key design goal of FuseJS is to keep your JS code small, clean and only concerned with the practical functions of your application. Meanwhile
all things UX-oriented such as layout, data presentation, animation and gesture response is left to declarative $(UX markup) and native UI components.

The way Fuse separates JavaScript business logic from UX markup presentation has some clear benefits:

* Performance - all the performance critical bits are handled in native code and based on native UI components.
* Easy - declarative code is easy to read, write and understand even with limited programming knowledge
* Less error prone - fewer states means less things can go wrong
  * Visual tooling - UX markup can be edited by Fuse tools such as inspectors, timelines and generally cool drag & droppy stuff.

Note that Fuse has tons of declarative APIs (designed UX markup) that replace the need for controlling animation from JavaScript (i.e. imperatively).

Many other JavaScript frameworks mix imperative UI code, animation and performance critical tasks into JavaScript, hence many people new to FuseJS tend to try
doing things this way in the beginning. While most of these things are technically possible in FuseJS, it is discouraged. We recommend taking some
time to study the Fuse examples to get a feel for the new way of doing things.

Purifying your code by separating view and logic into UX markup and JavaScript can shrink your code base significantly, make it more maintainable, and allow
more effective collaboration between UX designers and developers.

If you need to write performance-critical business logic, we recommend doing that in native code or alternatively @(Uno) code instead of JS.
