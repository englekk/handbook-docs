# $(Styling) and Resources

In Fuse, styling refers to setting properties such as fonts, colors, margins and appearances in a consistent way throughout your app.

## $(Style)

To add a `Style` to a `Node`:

	<StackPanel>
		<Style>
			<Text FontSize="32" />
		</Style>
		<Text>Biggie!</Text>
		<Panel>
			<Text>Another biggie!</Text>
		</Panel>
	</StackPanel>

The `Text` instances created at the level where the `Style` is introduced _or below_ will be affected by the `Style`. In this case, adding the `Style` will have the same appearance as adding the `FontSize` to all the `Text`-elements separately.

## $(Theme)

The `Theme` property of `App` decides whether your app is set up with *native UI controls* or *OpenGL ES powered graphics* as the root-level presentation technology, as well as the default look and feel of @(control).

For more information see @(Themes).

## $(Global resources)

Global resources are shared objects that are created once and used throughout your application. Global resources are recommended for objects that never change during the application lifetime, such as fonts, colors and sounds.

Global resources are created in UX markup by using the `ux:Global` attribute. `ux:Global` creates a global alias. This means you can refer to it by its `ResourceAlias`.

A resource alias is local to the type of the resource. If for example you have both a `float4` color resource named `Coral` and a `SolidColor` resource named `Coral`, Fuse will understand from context which is which.

This is how you import a TTF file and create a global resource from it:

	<Font File="HelveticaNeue-MediumCondensed.ttf" ux:Global="Helvetica" />

The above line can be placed in any file in your project, but you typically stuff these in your Style UX file or a special UX file dedicated to resources.

Once you've created a `ux:Global` alias, you can refer to your font by just the alias from anywhere in your code:

	<Text Font="Helvetica">Hello, yes, this is text.</Text>

## $(Resource)s and $(ux:Key)

To declare a Dynamic Resource, simply:

	<string ux:Key="WelcomeMessage" ux:Value="Hello!">

The resource will then be available to any nodes below it in the tree where it is defined. It can then be used as:

	<Text Value="{Resource WelcomeMessage}" />

### $(Localization)

> *Warning* : The feature that allows placing resources inside triggers, as seen below, is no yet rolled out, as of 0.9.5, but coming soon!

Localization of strings, values, icons and other resources can be easily achieved with *resources*.

    <StateGroup Active="{language}">
		<State Name="norwegian">
			<ResourceString Key="hello" Value="hei"/>
		</State>
		<State Name="english">
			<ResourceString Key="hello" Value="hello"/>
		</State>
		<State Name="spanish">
			<ResourceString Key="hello" Value="hola"/>
		</State>
	</StateGroup>

Then, instead of specifying the greeting string directly throughout the app, we can use a resource binding instead:

	<Text Value="{Resource Greeting}" />

* Note that we here use `ResourceString` instead of `string`. `ResourceString` is actually a behavior, which applies its value as a resource when rooted, which is why we can use it with a `StateGroup`.


<!--## The anatomy of controls

TODO: Explain appearances, overlays, child/parent, how

## Tweaking existing styles

- InheritStyle = false ? -->
