# $(Observable)s

Observable은 FuseJS 어플리케이션의 아주 중요한 모듈입니다. Observable은 반응형과 비동기 프로그래밍을 처리합니다.

또한, `Observable`은 퓨즈에서 UX와 자바스크립트간의 데이터 바인딩에 주로 사용됩니다.

Observable은 단일 값 또는 여러개의 요소를 포함한 리스트를 가질 수 있습니다. Observable의 value 속성이 변경되면 바인딩 된 영역에 그 값을 자동으로 통지합니다.

Observable로 데이터 주도형 UI 프로그래밍을 쉽게 구현하세요.

> ## Observable에 대한 소개 비디오

[YOUTUBE mi8sWErrabI]

### 모듈을 참조하는 방법

	var Observable = require('FuseJS/Observable');

### 기본적인 사용법

Observable 객체는 0 또는 여러개의 값을 이용해 `Observable` 함수를 호출하는 것으로 생성됩니다.

* `Observable(<initial values>)` - 생성자

예제:

	var emptyObservable = Observable();
	var isSomethingEnabled = Observable(true);

빈 observable 객체의 .value는 undefined이고, .length는 0입니다.

#### Observable의 value 속성

만약 observable이 하나의 값을 가지고 있다면, `.value` 속성을 이용해 값이 변경거나 가져올 수 있습니다:

	var someString = Observable('foobar');
	Console.Log(someString.value); // 'foobar'
	someString.value = 'barfoo'; // 값 설정

`.value`가 변경되면 바인딩 된 속성들에 자동으로 통지합니다.

#### 리스트형 Observable

observable을 리스트 형태로 이용하려면, `.add(item)`와 `.remove(item)` 같은 메소드를 이용하면 됩니다.
또한, 갯수를 알아내기 위해 `.length` 속성도 이용할 수 있습니다:

	var friends = Observable('Jake', 'Jane', 'Joe');
	Console.Log(friends.length); // 3

	friends.add('Gina');
	Console.Log(friends.length); // 4

#### Observable의 데이터 타입

Observable은 모든 기본적인 데이터 타입(number, string, boolean, array)을 지원합니다:

```
var obsNumber = Observable(10.5);// number
var obsString = Observable("hello");// string
var obsBool = Observable(true);// boolean
```

배열도 사용할 수 있습니다:

```
var obsRedColor = Observable([1,0,0,1]);
var obsWhiteAndBlack = Observable([1,1,1,1], [0,0,0,1]);
```

#### Observable의 놀랍고 재밌는 점

열마디 말보다 한 줄의 코딩이 낫죠.

예제:

	var firstName = Observable('John');
	var lastName = Observable('Doe');

	var fullName = Observable(function() {
		return firstName.value + ' ' + lastName.value;
	})

`firstName` 또는 `lastName`이 변경되면, `fullName`이 자동으로 업데이트 됩니다.

완전 마법이죠.

## $(Observable.Members:Members)

### $(Value operators)

#### $(Observable.value:value)

Gets or sets the current value of the @(Observable).

The `value` property acts as a shorthand for `getAt(0)` and `replaceAt(0)`.
It is most often used with single value Observables, although this is not a requirement.

	if (isSomethingEnabled.value) {
		doSomething();
	}
	isSomethingEnabled.value = false;

#### $(Observable.toArray:toArray)

Returns a copy of the Observables internal values array.

	var obs = Observable(1,2,3);
	var obsArray = obs.toArray(); //obsArray == [1,2,3]

### List operators

#### $(Observable.length:length)

Returns the number of values in the @(Observable)

	var fruits = Observable('Orange', 'Apple', 'Pear');
	Console.Log(fruits.length); //output: 3

#### $(Observable.getAt:getAt(index))

Returns the value at the given `index`

	var seasons = Observable('Summer', 'Fall', 'Winter', 'Spring');
	Console.Log(seasons.getAt(2)); //output: 'Winter'

#### $(Observable.add:add(value))

Adds `value` to the @(Observable:observables) list of values.

	var colors = Observable('Red', 'Green');
	colors.add('Blue');

#### $(Observable.remove:remove(value))

Removes the first occurence of `value` from the @(Observable:observables) list of values.

	var shapes = Observable('Round', 'Square', 'Rectangular');
	shapes.remove('Rectangular');

#### $(Observable.tryRemove:tryRemove(value))

Tries to remove the first occurence of `value` from the @(Observable:observables) list of values.
Returns true if successful, and false otherwise.

	var shapes = Observable("Round", "Square", "Rectangular");
	if(shapes.tryRemove("Rectangular")) {
		Console.Log("success");
	}

#### $(Observable.removeAt:removeAt(index))

Remove the value at the given `index`.

	var shapes = Observable('Round', 'Square', 'Rectangular');
	shapes.removeAt(2);

#### $(Observable.removeWhere:removeWhere(func))

Removes all values for which `func` is true.

	var hotPlaces = Observable(
		{name: "Oslo", temperature: 30},
		{name: "New York", temperature: 24},
		{name: "California", temperature: 27},
		{name: "Sydney", temperature: 10}
	).removeWhere(function(place){
		return place.temperature < 20;
	}); //Removes Sydney from the list

#### $(Observable.forEach:forEach(func(item)))

Invokes `func` on every value in the @(Observable).

	var numbers = Observable(10, 2, 50, 3);
	numbers.forEach(function(number) {
		Console.Log(number + " is a nice number!");
	});

#### forEach(func(item,index))

Invokes `func` on every value in the @(Observable).

	var numbers = Observable(10, 2, 50, 3);
	numbers.forEach(function(number, index) {
		Console.Log(number + " has the index: " + index);
	});

If `func` accepts two arguments, the second argument is the index of that item in the @(Observable).

#### $(Observable.replaceAt:replaceAt(index, value))

Replaces the value at `index` with `value`

	var ingredients = Observable('sugar', 'milk', 'potato');
	ingredients.replaceAt(2, 'flour'); //Replaces 'potato' with 'flour'

#### $(Observable.replaceAll:replaceAll(array))

Replaces the Observables values with values from the `array`.

	var colors = Observable("Red", "Green", "Blue");
	colors.replaceAll(["Orange", "Cyan", "Pink"]);

#### $(Observable.clear:clear())

Removes all values from the @(Observable).

	var colors = Observable("Red", "Green");
	colors.clear();

#### $(Observable.indexOf:indexOf(value))

Returns the index of the first occurrence of `value`.

	var seasons = Observable("Summer", "Fall", "Winter", "Spring");
	var index = seasons.indexOf("Winter"); // 2

#### $(Observable.contains:contains(value))

Returns true if `value` exists in the `var`.

	Observable seasons = Observable("Summer", "Fall", "Winter", "Spring");
	var winterExists = seasons.contains("Winter"); // true

#### $(Observable.refreshAll:refreshAll(newValues, compareFunc, updateFunc, mapFunc))

Updates all items in the @(Observable) with the values from `newValues`.
`compareFunc` is used to determine whether two items are equal. `updateFunc` is used to update an existing item with new values when a match is found by `compareFunc`.
`mapFunc` is called whenever a new item is found and allows it to be mapped to a new object.

	var items = Observable({
		{id: 1, text: "one" },
		{id: 2, text: "two" },
		{id: 3, text: "tres" },
	})
	var newItems = [
		{id: 3, text: "three" },
		{id: 4, text: "four" },
		{id: 5, text: "five" }
	]
	items.refreshAll(newItems,
		//Compare on ID
		function(oldItem, newItem){
			return oldItem.id == newItem.id;
		},
		// Update text
		function(oldItem, newItem){
			oldItem.text.value = newItem.text;
		},
		// Map to object with an observable version of text
		function(newItem){
			return {
				id: newItem.id,
				text: Observable(newItem.text)
			};
		}
	);


### $(Reactive operators)

FuseJS comes with set of reactive operators that return @(Observable:Observables) from other @(Observable:Observables). This means that if the original `Observable` changes, any `Observable`s that are created as a result of applying a reactive operator will also change automatically.

> Note! It is important to understand that the result of a reactive operator will only be computed if the resulting `Observable` is *consumed*, i.e. databound and its value is needed. If you @(Observable.map:map(func)) over an `Observable` collection and try to `console.log` from the mapping function, these contents might not be displayed because the resulting `Observable` is not databound. If you run into this problem, you can manually add a subscriber for debugging purposes, as described @(Observable.addSubscriber:here).

#### $(Observable.where:where(condition))

Returns a new @(Observable) with only the values for which `condition` returns true.

The new @(Observable:observable) observes the old @(Observable:observable), and will therefore update whenever a value changes in the original observable.

	fruits = Observable(
		{ name: 'Apple' , color: 'red'    },
		{ name: 'Lemon' , color: 'yellow' },
		{ name: 'Pear'  , color: 'green'  },
		{ name: 'Banana', color: 'yellow' });

	goodFruits = fruits.where(function(e){
		return e.color === 'yellow';
	});


If the `condition` returns an `Observable`, the `where` operator will also observe the condition.

#### $(Observable.map:map(func(item)))

Invokes `func` on every value in the @(Observable) returning a new @(Observable) with the results.

	var numbers = Observable(1, 4, 9);
	var roots = numbers.map(function(number) {
		return Math.sqrt(number);
	});

The values of `roots` becomes the square root of the numbers in `numbers`. The values in `numbers` remain unchanged.

#### $(Observable.mapi:map(func(item, index)))

Invokes `func` on every value in the @(Observable) returning a new @(Observable) with the results.

	var numbers = Observable("this", "item", "is");
	var roots = numbers.map(function(item, index) {
		return item + " has the index nr: " + index;
	});

When Observable.map is used with a function which takes two arguments, the second argument is the index of that item in the @(Observable).

#### $(Observable.count:count())

Returns the number of values in the @(Observable) as an observable number. Whenever an item is added or removed from the @(Observable), the `count` changes.

	books = Observable("UX and you",
		"Observing the observer",
	    "Documenting the documenter");
	numBooks = books.count(); //result: 3

#### $(Observable.countCondition:count(condition))

Returns an observable number of values for which `condition` is true.

	var tasks = Observable(
		{ title: "Learn Fuse", isDone: true },
		{ title: "Learn about Observables", isDone: true },
		{ title: "Make awesome app", isDone: false }
	);
	var tasksDone = tasks.count(function(x){
		return x.isDone;
	}); // 2

#### $(Observable.not:not())

Returns an @(Observable) that has the inverse value of the @(Observable) you are calling it on. If the @(Observable) is `true`, the returned one will be `false`, and vice versa.

	falseValue = Observable(false);
	trueValue = falseValue.not();

<!--
#### $(Observable.inner:inner())
TODO: Write inner -->

#### $(Observable.filter:filter(condition))

Returns an observable that will only propagate values that pass the given `condition`, otherwise it retains its previous value.

This method only considers the first (single) value of an observable.

<!-- TODO: Make example -->

#### $(Observable.expand:expand(func))

When an @(Observable) contains only a single array, expand will return an @(Observable) containing the values from that array.

Observable([1,2,3]).expand() -> Observable(1,2,3)


### Subscribing to updates

#### $(Observable.addSubscriber:addSubscriber(func))

To manually react to changes in an @(Observable), one can use the `addSubscriber` method.
`func` will be run whenever the @(Observable) changes.

#### $(Observable.removeSubscriber:removeSubscriber(func))

When you are done consuming the values from the Observable, it is important to clean up by removing
the subscription, otherwise we can accumulate memory garbage over time:

	username.removeSubscriber(usernameLogger);

### Other

#### $(Observable.depend:depend())

#### $(Observable.failed:failed(message))

#### $(Observable.setValueExclusive:setValueExclusive(value, excludingObserver))

Sets the value of the @(Observable) without notifying `excludingObserver`

	var observable = Observable(1);

    var shouldGetNotification = function() { }
	var shouldNotGetNotification = function() { }

	observable.addSubscriber(shouldGetNotification);
	observable.addSubscriber(shouldNotGetNotification);

	observable.setValueExclusive(2, shouldNotGetNotification);

#### $(Observable.toString:toString())

Returns a string representation of an observable and its contents.

	var testObservable = Observable(1, "two", "3");
	testObservable.toString(); // "(observable) 1,two,3"

## Topics

<!-- TODO: Fill inn here!! -->

Observables can be used for a lot of things:

* @(Data Binding)
* Asynchronous programming
* Reactive programming

<!-- TODO: Here are articles about each topic -->
