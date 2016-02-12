# Working with foreign code

While we're trying to wrap as much native cross-platform functionality as we can into Fuse, there are naturally going to be features that haven't been implemented yet.
This guide explains how you can use the Foreign Code feature in Uno to reach into your platform's native functionality yourself.

_Note:_ This is a young feature, intended to replace the old way of doing interop through language bindings. If you want to know more about our reasons for doing so, we have written a [standalone post](https://medium.com/@fusetools/a-sane-way-of-mixing-languages-in-fuse-660b351c2f96) on the subject.


## What is Foreign Code?

Foreign Code allows you to write a Uno method body in a different language. Consider the following example:

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

Here we wanted to avoid reinventing the wheel and instead use Java and Objective-C's own Logging. By tagging our Uno method with the `Foreign` attribute, and picking a language, we are then able to write the chosen language between the `@{` & `@}` braces.

You are probably wondering why we have strange braces around our foreign method body. This simply tells the compiler "Don't try and read this bit as Uno".

The two languages we currently support in the `Foreign` attribute are `Objective-C` (on iOS) and `Java` (on Android) which you write as `[Foreign(Language.ObjC)]` and `[Foreign(Language.Java)]` respectively.

The `Foreign` attribute lives in the `Uno.Compiler.ExportTargetInterop` namespace, so make sure you import that with `using` at the top of your Uno file.

The eagle-eyed among you will also have noticed that we have two methods with the same name and signature in the same class. We can do this since `extern(...)` strips out code that does not match the target platform in a very early stage of compilation.


## Type Conversion

One of the more mind-numbing things to deal with when using some 'foreign function interfaces' is dealing with types.

In Foreign Code arguments and return values are automatically converted between their Uno representation and their corresponding Java/Objective-C representation.


#### Primitive Types

Primitive types (`int`, `float`, `char`, etc) are converted as you would expect with the one exception that java does not have primitive unsigned bytes, so a `byte` in java is an `sbyte` in Uno.

So you can write this:

```
	[Foreign(Language.Java)]
	public static extern(Android) void Foo(double x, long y)
	@{
		android.util.Log.d("ForeignCodeExample", "Well look at this:" + x + "and" + y);
	@}
```

And call it in Uno like this:

```
    Foo(1.2, 12345678);
```

#### Strings

Strings are just too common for us not to handle automatically. So Uno strings map to:

- `java.lang.String` in Java
- `NSString*` in Objective-C

So again you can write something like this:

```
	[Foreign(Language.ObjC)]
	public static extern(iOS) void Log(string message)
	@{
		NSLog(@"%@", message);
	@}
```

And calling it in Uno like this:

```
    Log("Type magic everywhere!");
```

#### Foreign objects passed to Uno

So what happens when we want to return a Java or Objective-C object from a foreign method? The answer is you get an opaque object representing the foreign type.

For Objective-C you get a `ObjC.Object` and for Java you get a `Java.Object`.

You may wonder why not try and map more types; it's probably easy to think of a few more types that it would be nice to have converted automatically. The full answer for this can be found the [medium article](https://medium.com/@fusetools/a-sane-way-of-mixing-languages-in-fuse-660b351c2f96) mentioned earlier. To summarize:

> Mapping object models in the general case does not work, even in languages as syntactically similar as Java and Uno. The differences necessary in a translation are a mental overhead for the programmer using Foreign Code. We choose instead to provide a predicable interface and require that working with the internals of a foreign object is done in foreign code.

Here's how it looks like to get an object from Java:

```
	[Foreign(Language.Java)]
	public static extern(Android) Java.Object Test(int texName)
	@{
        return new android.graphics.SurfaceTexture(texName);
	@}
```

And calling it:

```
    var stex = Test(1);
```

#### And the other way? (Uno objects passed into Foreign Code)

Predictably we take the same approach. An Uno passed to Java or Objective-C is boxed inside an opaque object.

In Objective-C the type of that box is `id<UnoObject>` and in Java it is `com.uno.UnoObject`


Here is an example of an Uno object being passed in:

```
	[Foreign(Language.Java)]
	public static extern(Android) void Test(SomeFancyType soFancy)
	@{
        // Inside here the type of soFancy is `UnoObject`
		android.util.Log.d("ForeignCodeExample", "Here it is" + soFancy);
	@}
```

Call it is how you would expect:

```
    var v = Test(new SomeFancyType(1, 2, 3));
```

#### Ok but what about passing a `Java.Object` back to Java or a `ObjC.Object` back to Objective-C?

We just do the sane thing and un-box the foreign objects again, so no need to worry about some box-of-box-of-box of some Java type!

Just to clarify:
- An `ObjC.Object` passed back to Objective-C becomes an `id`
- A `Java.Object` passed back to Java becomes an `Object`

Of course you can still cast the objects back to their original types.


#### Arrays?

See the `Coming Soon` section. :)


#### Delegates

In the current release this feature is available in Objective-C, and the Java version is on the way. Please see the `Coming Soon` section for details.


#### A note on objects from the old bindings

Rather than just remove the old style bindings and force you into Foreign Code immediately, we are taking a more measured approach.

Any object created using the old bindings can be passed up to Java in the same way as `Java.Object`.
Objective-C bindings objects can be passed as `ObjC.Object`.



## Talking back to Uno

Sometimes it is not enough to be able to return values from Foreign Code to Uno. Sometimes it makes more sense to interact with Uno from inside Foreign Code. We need to be very clear to tell the compiler what parts of our Foreign Code are actually calls into Uno. To do this we use our UXL syntax.

Using UXL we can do two main things from Foreign Code:

- Access Uno fields
- Call Uno methods

If you haven't seen UXL before, and/or are someone who likes getting all the details on something, then check out our fairly extensive [UXL Handbook](/learn/guides/uxl-handbook#uxl-macros). For this guide however we will just show lots of small examples:


### `Get` and `Set` Static Uno Fields

_Note:_ Woops, looks like we have a bug with Foreign Code accessing static fields on Android. This will be fixed in an upcoming release. Once it is out this note will be removed

Let's start nice and simple. We can get or set the value of a property using the `Get` and `Set` macros.

The anatomy of a UXL `Get` expression is as follows:

```
v¯¯¯¯¯¯¯¯¯¯v¯ The `@{` `}` syntax means its a uxl macro
@{Foo:Get()}
   ^
 field name
```

And for `Set` expressions it looks like thus

```
          v¯¯¯¯¯¯ this is a foreign value
@{Foo:Set(20)}
   ^
 field name
```

Let's see this in some example code:


```
using Uno.Compiler.ExportTargetInterop;

public class Example
{
	public static int SomeValue = 7;

	[Foreign(Language.Java)]
	public extern(Android) void Doubler()
	@{
		int originalVal = @{SomeValue:Get()};
		@{SomeValue:Set(originalVal * 2)};
	@}

	[Foreign(Language.ObjC)]
	public extern(iOS) void Doubler()
	@{
		int originalVal = @{SomeValue:Get()};
		@{SomeValue:Set(originalVal * 2)};
	@}
}
```


### `Get` and `Set` Uno Instance Fields

If you have an instance method you would expect it to have a `this` pointer. Of course `this` is a reserved name in most languages so we have to use something else.

Foreign instance methods have access to `_this`, which refers to the object they are called on, wrapped as `UnoObject`.

Since the type can't automatically be inferred from foreign data, you have to use the `:Of` macro to interact with it:

```
@{MyClass:Of(_this)}
```

The above code simple says *"Hey, the Uno type of `_this` is `MyClass`"*

So let's see how we use this is a similar example to the last one:

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
		NSString* originalString = @{Example:Of(_this).MyString:Get()};
		NSString* newString = [originalString stringByAppendingString: @"!!!"];
		@{Example:Of(_this).MyString:Set(newString)};
	@}
}
```

### Calling Static Uno Methods

UXL's `Call` macro lets you call Uno methods (and other foreign methods) from inside a foreign method.

Once again let's start with a little anatomy:

```
 the uno method name          The foriegn values to be passed as arguments
      v                         v  v
@{Foobernator(int,string):Call(1, "jam")}
               ^    ^
     The uno types of the arguments
        of the method 'Foobernator'

```

Let's see a simple example of this is action:

```
using Uno.Compiler.ExportTargetInterop;

public class Example
{
	public void Angrify (string str)
	{
		debug_log "ARGHHH! " + str + "ARGGHHH!";
	}

	[Foreign(Language.Java)]
	public extern(Android) void Rage()
	@{
		@{Angrify():Call("JAVA!")};
	@}

	[Foreign(Language.ObjC)]
	public extern(iOS) void Rage()
	@{
		@{Angrify():Call(@"OBJECTIVE-C!")}; // :p
	@}
}
```

### Calling Uno Instance Method

I bet at this point you can see exactly where this is going! Time to use the `Of` macro with `_this` again:

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
		NSString* deviceModel = [[UIDevice currentDevice] model];

		// Call instance method
		@{Example:Of(_this).SetDeviceModel(string):Call(deviceModel)};

		// Call static method
		@{Example.Log(string):Call(deviceModel)};
	}
}
```


## External source files

A lot of Fuse's philosophy revolves around the idea of 'using the right tool for the right job'. Foreign Code makes it really easy to call into java to get something done fast. However we also believe that, if you need to write a lot of java, it's best to do it in java. To this end, we now let you add `.java`, `.mm` & `.hh` files to your `.unoproj` to get them included in the build.

You add Java and Objective-C files like this:

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

Note that the wildcard (`*`) only includes fuse files by default, you have to explicitly add foreign files. This is super helpful as it means you can have a valid Java (or Objective-C) project inside you app hierarchy (with tests etc) and only include the files you actually need in your app.


### Java

With Java you normally have to worry about your folder structure matching your package hierarchy, but not in Fuse. We will parse the `package` statement in your java files and ensure the hierarchy is correct in the project emitted by our compiler.

_Note:_ With foreign Java, there is currently no way to `import` a package or class. For that reason you have to reference classes by their fully qualified name.


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

--------------------------------------------------------------------------------

# Coming Soon!

As mentioned before, Foreign Code is a young feature and we are excited to discuss with you where and how it could grow.

But here are a could of things coming down 'the pipes' soon.


## Arrays

This feature allows you to pass Uno arrays to Foreign Code. As ever, we are performance conscious so we don't just make a copy of the data straight away. We pass a handle to the Uno array that you can use directly in your foreign code. If you need a full copy of the data just call `arr.copyArray()` (in Java) or `[arr copyArray]` (in Objective-C) and you will be given the native version of that type.

Full details will be available in this document when this feature is released.


## Delegates

Foreign Code will also allow you to pass delegates to your foreign methods. Whilst the end result is the same, the details vary a little between Java & Objective-C so let's tackle them separately:

**Objective-C:**

We have preliminary support for delegates in Objective-C, but there are known bugs that will be fixed in future releases.

Delegates get converted to an Objective-C block of the corresponding type. As an example, an argument of the type `Action<string, int>` becomes a block of type `^ void(NSString*, int)`. The argument and return type of the block use the same type conversions as arguments to foreign functions normally do.

Here is a simple example of this in action:

```
[Foreign(Language.ObjC)]
public static extern(iOS) double DelegateArgument(Func<int, double> f)
@{
	 // f is of type `^ double(int)` here, so can be called like any function
	 return f(12);
@}
```

**Java:**
We have added this internally and it will be out in a release soon! This will allow you to pass `Action`s, `Func`s & user defined delegates to Java in the same way as you can today with Objective-C.

Full details will be available in this document when this feature is released.


## Foreign support libraries

There are many platform specific things you end up doing quite frequently that should be easier in Foreign Code. For example in Java you often want to get the `Context` for the application. We are going to slowly provide support libraries around Foreign Code to make things like this much easier.

Watch this space!
