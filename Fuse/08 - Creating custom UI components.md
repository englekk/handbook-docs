# $(Creating custom UI components)

## ux:Class

You can introduce a new _class_ at any point in your code using ux:Class. The following code creates a class called `Header`, which inherits from `Text`.

```
<Text ux:Class="Header" FontSize="22" />
```

It can be instantiated, just like any other Fuse element.

```
<Header>Welcome</Header>
```

In this case, the `Header`-class will be a distinctly different class from `Text`, although all properties from `Text` will also be available. Note that the class can be defined in a separate file, and often is.

Custom classes can be as complex as you want:

```
<Image ux:Class="BurgerIcon">
	<MultiDensityImageSource>
		<FileImageSource File="Burger.png" Density="1"/>
		<FileImageSource File="Burger.png@2x.png" Density="2"/>
	</MultiDenistyImageSoruce>
</Image>

<!-- ... -->

<Panel>
	<BurgerIcon />
</Panel>
```

They may also contain their own "logic" using @(Triggers:triggers), @(Actions:actions), `JavaScript` and so on.

Note that classes created with ux:Class map to real Uno-classes:

```
<Panel ux:Class="MyPanel"/>
```

would be equal to the following Uno-class:

```
public class MyPanel : Panel { ... }
```

## ux:Property

In most cases, when creating custom classes, we need to be able to define some interface to that class. This can be done through UX using the ux:Property syntax.

__MyButton.ux__:

```
<Panel ux:Class="MyButton" ux:Name="self">
    <string ux:Property="Text" ux:Value="MyButton" />
    <float4 ux:Property="CornerRadius" ux:Value="0" />
    <float4 ux:Property="BackgroundColor" ux:Value="#f00" />
    <float4 ux:Property="TextColor" ux:Value="#000" />
    <Text Alignment="Center" TextColor="{Property self.TextColor}" Value="{Property self.Text}"/>
    <Rectangle Layer="Background" CornerRadius="{Property self.CornerRadius}">
        <SolidColor Color="{Property self.BackgroundColor}"/>
    </Rectangle>
</Panel>
```

Here we have created a new class called MyButton. We expose four properties (Text, CornerRadius, BackgroundColor and TextColor). These properties can then be bound to within our ux:Class using the `{Property self.PropName}` syntax.

When we instantiate our class, we can then access these properties directly:

__MainView.ux__:
```
<App>
	<Panel>
		<MyButton CornerRadius="20" Text="MyText" TextColor="#fff"
			      BackgroundColor="#5C6BC0 " Width="200" Height="50"/>
	</Panel>
</App>
```

- Note: ux:Property currently only works for atomic types (eg. float4, bool and string) and not for complex class types. This will be improved in an upcoming version.
- Note: property-bindings require that the source and target properties are of identical types.
