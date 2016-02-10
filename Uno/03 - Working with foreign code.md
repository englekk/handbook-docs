# Working with foreign code

While we're trying to wrap as much native cross-platform functionality as we can into Fuse, there are naturally going to be features that haven't implemented yet.
This guide explains how you can use the foreign code feature in Uno to implement reach into your platform's native functionality yourself.

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

Here we wanted to avoid reinventing the wheel and instead use Java and Objective-C's own Logging. By tagging our uno method with the `Foreign` attribute, and picking a language, we are then able to write the chosen language between the `@{` & `@}` braces.

You are probably wondering why we have strange braces around our foreign method body. This simply tells the compiler "Don't try and read this bit as uno" .

The two languages we currently support in the `Foreign` attribute are `Objective-C` (on iOS) and `Java` (on android) which you write as `[Foreign(Language.ObjC)]` and `[Foreign(Language.Java)]` respectivly.

The `Foreign` attribute lives in the `Uno.Compiler.ExportTargetInterop` namespace, so make sure you import that with `using` at the top of your uno file.

The eagle-eyed among you will also have noticed that we have two methods with the same name and signature in the same class. We can do this since `extern(...)` strips out code that does not match the target platform in a very early stage of compilation.


## Type Conversion

One of the more mind-numbing things to deal with when using some 'foreign function interfaces' is dealing with types.

In Foreign Code arguments and return values are automatically converted between their Uno representation and their corresponding Java/Objective-C representation.


#### Primtive Types

Primitive types (`int`, `float`, `char`, etc) are converted as you would expect with the one exception that java does not have primitive unsigned bytes, so a `byte` in java is an `sbyte` in uno.


#### Strings

Strings are just too common for us not to handle automatically. So uno strings map to:

- `java.lang.String` on Java
- `NSString*` on Objective-C


#### Foreign objects passed to Uno

So what happens when we want to return a Java or Objective-C object from a foreign method? The answer is you get an opaque object representing the foreign type.

So for Objective-C you get a `ObjC.Object` and for Java you get a `Java.Object`.

You may wonder why not try and map more types, it's probably easy to think of a few more types that it would be nice to have converted automatically. The fullest answer for this can be found the [medium article](https://medium.com/@fusetools/a-sane-way-of-mixing-languages-in-fuse-660b351c2f96) mentioned earlier. However to summarize:

> Mapping object models in the general case does not work, even in languages as syntactically similar as Java and Uno. The differences neccesary in and translation are a mental overhead for the programmer using Foreign Code. We choose instead to provide a predicable interface and require that working with the internals of a foreign object is done in foreign code.


#### And the other way? (Uno objects passed into Foreign Code)

Predicatably we take the same approach. An Uno passed to Java or Objective-C is boxed inside an Opaque object.

In Objective-C the type of that box is `UnoObject` and in Java it is `com.uno.UnoObject`


#### Ok but what about passing a `Java.Object` back to Java or a `ObjC.Object` back to Objective-C?

We just do the sane thing and un-box the foreign objects again, so no need to worry about some box-of-box-of-box of some Java type!

Just to clarify:
- An `ObjC.Object` passed back to Objective-C becomes an `id`
- A `Java.Object` passed back to Java becomes an `Object` (but of course you can still cast it back to it's original java type.


#### Arrays?

See the `Coming Soon` section :)


#### Delegates

Foreign Code also allows you to pass delegates to your foreign methods. Whilst the end result is the same, the details vary a little between Java & Objective-C so lets tackle them seperately:

**Objective-C:**

Delegates get converted to an Objective-C block of the corresponding type. As an example, an argument of the type `Func<string, int, object>` becomes a block of type `^ id<UnoObject>(NSString*, int)`. The argument and return type of the block use the same type conversions as arguments to foreign functions normally do.

Here is a simple example of this in action:

```
????
```

**Android:**

In the current release this feature is only avaiable in Objective-C, but the Java version is on the way. Please see the `Coming Soon` section for details.


#### A note on objects from the old bindings

Rather than just remove the old style bindings and force you into foreign code immediately, we are taking a more measured approach.

Any object created using the old bindings can be passed up to java in the same way as `Java.Object`


## Talking back to Uno

Sometimes it is not enough to be able to return values from Foreign Code to Uno. Sometimes it makes more sense to intereact with uno from inside Foreign Code. We need to be very clear to tell the compiler what parts of our foreign code are actually calls into Uno. To do this we use our UXL syntax.

Using UXL we can do two main things from Foreign Code:

- Access uno fields
- Call uno methods

If you havent seen UXL before, and/or are someone who likes getting all the details on something, then check out our fairly extensive [UXL Handbook](/learn/guides/uxl-handbook#uxl-macros). For this guide however we will just show lots of small examples:


### `Get` and `Set` Static Uno Fields

Let's start nice and simple. We can get or set the value of a property using the `Get` and `Set` macros.

The anotomy of a UXL `Get` expression is as follows:

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

Now if you have an instance method then you would expect ot have a `this` pointer. Of course `this` is a reserved name in most languages so we have to use something else.

So instead Foreign instance methods have access to `_this`, which refers to the object they are called on, wrapped as `UnoObject`.

Since the type can't automatically be inferred from foreign data, you have to use the `:Of` macro to interact with it:

```
@{MyClass:Of(_this)}
```

The above code simple says *"Hey, the Uno type of `this` is `MyClass`"*

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
        @{Angrify():Call(@"JAVA!")}; // :p
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
		NSString *deviceModel = [[UIDevice currentDevice] model];

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

With Java you normally have to worry about your folder structure matching your package hierarchy, but not in Fuse. We will parse the `package` statement in your java files and ensure the hieracrchy is correct in the project emmited by our compiler.

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

--------------------------------------------------------------------------------

# Coming Soon!

As mentioned before, Foreign Code is a young feature and we are excited to discuss with you where and how it could grow.

But here are a could of things coming down 'the pipes' soon.


## Arrays

This feature allows you to pass uno arrays to foreign code. As ever, we are performance conscious so we don't just make a copy of the data straight away. We pass a handle to the Uno array that you can use directly in your foreign code. If you need a full copy of the data just call `.copyArray()` (in Java) or `????` (in Objective-C) and you will be given the native version of that type.

Full details will be available in this document when this feature is released


## Delegates in Java

We have added this internally and will be out in a release soon! This will allow you to pass Actions, Funcs & user defined delegates to Java in the same way as you can today with Objective-C.

Full details will be available in this document when this feature is released


## Foreign Support Libraries

There are many platform specific things you end up doing quite frequently that should be easier in foreign code. For example in Java you often want to get the `Context` for the application. We are going to slowly provide support libraries around foreign code to make things liek this much easier.

Watch this space!
