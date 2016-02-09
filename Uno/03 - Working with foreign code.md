# Working with foreign code

While we're trying to wrap as much native cross-platform functionality as we can into Fuse, there are several native features that haven't implemented yet.
This guide explains how you can use foreign code in Uno to implement native functionality yourself.

_Note:_ This is a very early feature, intended to replace the old way of doing interop through language bindings. If you want to know more about our reasons for doing so, we have written a [standalone post](https://medium.com/@fusetools/a-sane-way-of-mixing-languages-in-fuse-660b351c2f96) on the subject.

## What is foreign code?

Foreign code allows you to write a Uno method body in a different language. Consider the following example:

```
using Uno.Compiler.ExportTargetInterop;

class Example
{
	[Foreign(Language.Java)]
	public static extern(Android) void Log(string message)
	@{
		android.util.Log.d("ForeignCodeExample", message);
	@}

	[Foreign(Language.ObjC)]
	public static extern(iOS) void Log(string message)
	@{
		NSLog(@"%@", message);
	@}
}
```

A method is marked as foreign with the `Foreign` attribute, which specifies which language the method is written in – either `Language.Java` or `Language.ObjC`.
This attribute lives in the `Uno.Compiler.ExportTargetInterop` namespace, so make sure you import that.
You also need to specify the method body inside _alpha braces_: `@{`  ...  `@}`.

You may also have noticed that we have two methods with the same name and signature in the same class. We can do this since `extern(...)` strips out code that does not match the target platform in a very early stage of compilation.

_Note:_ With foreign Java, there is currently no way to `import` a package or class. For that reason you have to reference classes by their fully qualified name.

## Type conversion

Arguments and return values are automatically converted between their Uno representation and their corresponding Java/Objective-C representation.

Primitive types (`int`, `float`, `char`, etc) are converted as you would expect. For more complex types, refer to the following tables:

<table>
	<thead>
		<tr> <th>Uno</th> <th>Java</th> </tr>
	</thead>
	<tbody>
		<tr> <td><code>string</code></td> 		<td><code>String</code></td> </tr>
		<tr> <td><code>Java.Object</code></td> 	<td><code>Object</code></td> </tr>
		<tr> <td><code>object</code></td> 		<td><code>UnoObject</code></td> </tr>
	</tbody>
</table>

<table>
	<thead>
		<tr> <th>Uno</th> <th>Objective-C</th> </tr>
	</thead>
	<tbody>
		<tr> <td><code>string</code></td> <td><code>NSString *</code></td> </tr>
		<tr> <td><code>ObjC.Object</code></td> <td><code>id</code></td> </tr>
		<tr> <td><code>object</code></td> <td><code>id&lt;UnoObject&gt;</code></td> </tr>
		<tr>
			<td><code>Func&lt;string, int&gt;</code></td>
			<td><code>^ int(NSString *)</code></td>
		</tr>
		<tr>
			<td><code>Action&lt;object&gt;</code></td>
			<td><code>^ void(id&lt;UnoObject&gt;)</code></td>
		</tr>
	</tbody>
</table>

## UXL macros

You can use UXL macros in foreign methods, which essentially acts as a bridge into Uno-land.
We won't go into detail here, but rather provide some examples of the most common use cases.
For a complete reference on UXL macros, see the [UXL Handbook](/learn/guides/uxl-handbook#uxl-macros).

Foreign instance methods have access to `_this`, a wrapped version of the object they are called on.
Since the type can't automatically be inferred, you have to use the `:Of` macro to interact with it:

```
@{MyClass:Of(_this)}
```

<!-- AUTH: rewrite this sentence -->
We can then combine this with more UXL macros.

### `Get` and `Set`

We can get or set the value of a property using the `Get` and `Set` macros.

```
using Uno.Compiler.ExportTargetInterop;

public class Example
{
	public string MyString = "This is all native";

	[Foreign(Language.Java)]
	public extern(Android) void AddExcitement()
	@{
		String originalString = @{Example:Of(_this).MyString:Get()};
		String newString = originalString + "!!!";
		@{Example:Of(_this).MyString:Set(newString)};
	@}

	[Foreign(Language.ObjC)]
	public extern(iOS) void AddExcitement()
	@{
		NSString *originalString = @{Example:Of(_this).MyString:Get()};
		NSString *newString = [originalString stringByAppendingString: @"!!!"];
		@{Example:Of(_this).MyString:Set(newString)};
	@}
}
```

### `Call`

`Call` lets you call both foreign and Uno methods from inside foreign methods.
In this case, you also have to specify the method signature (with Uno types) as part of the macro.

```
using Uno.Compiler.ExportTargetInterop;

class Example
{
	string deviceModel;

	static void Log(string message)
	{
		debug_log(message);
	}

	void SetDeviceModel(string model)
	{
		deviceModel = model;
	}

	[Foreign(Language.Java)]
	extern(Android) void LogDeviceModel()
	{
		String deviceModel = android.os.Build.MODEL;

		// Call instance method
		@{Example:Of(_this).SetDeviceModel(string):Call(deviceModel)};

		// Call static method
		@{Example.Log(string):Call(deviceModel)};
	}

	[Foreign(Language.ObjC)]
	extern(iOS) void LogDeviceModel()
	{
		NSString *deviceModel = [[UIDevice currentDevice] model];

		// Call instance method
		@{Example:Of(_this).SetDeviceModel(string):Call(deviceModel)};

		// Call static method
		@{Example.Log(string):Call(deviceModel)};
	}
}
```

## External source files

Writing a lot of code with foreign methods and UXL macros can get quite tiring in the long run.
Because of this, you have the option to reference external source files.

You have to explicitly add them to your `unoproj`, like so:

```
{
	...

	"Includes": [
		"*",
		"Example.hh:CHeader:iOS",
		"Example.mm:CSource:iOS",
		"Example.java:Java:Android"
	]
}
```

### Java

With Java, you don't have to worry about folder structure. Uno will parse the `package` statement in your java files and create it for you.

### Objective-C

To use external Objective-C headers, you need to include them in your class, like so:

```
using Uno.Compiler.ExportTargetInterop;

[Require("Source.Include", "Example.hh")]
class Example
{
	...
}
```

_Note:_ Beware of naming collisions! An Objective-C class can't have the same name as an Uno class in the global namespace.


> ## Troubleshooting

When calling a Uno method from a foreign Objective-C method in a different thread, you may experience a crash if the block you're calling from doesn't have an autorelease pool.

You can create an autorelease pool at the beginning of the block, like this:

```
[someObjCObject doSomethingInSeparateThreadWithCallback: ^void () {
	uAutoReleasePool autoreleasePool;

	@{SomeUnoClass.SomeStaticMethod():Call()};
}];
```

_Note:_ you need to `#include<Uno/Memory.h>` to get access to `uAutoReleasePool`.


Another alternative is to pass a `delegate`, `Func` or `Action`, which will create the autorelease pool for you:

```
class SomeClass
{
	[Foreign(Language.ObjC)]
	extern(iOS) void DoSomethingInSeparateThread(Action<> callback)
	@{
		[someObjCObject doSomethingInSeparateThreadWithCallback: callback];
	@}
}
```