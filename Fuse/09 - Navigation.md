# $(Navigation)

Most apps contains more than just one page. Fuse has several components for making and customizing navigation between different pages.

Lets take a look at a simple case: we want a set of @(Page:pages) which we can @(SwipeNavigate:swipe) between.

## $(Basic navigation)

To start off, we make a set of @(Panel:panels) which represents our @(Page:pages).

	<Panel>
		<Panel Background="Red"/>
		<Panel Background="Blue"/>
		<Panel Background="Green"/>
	</Panel>

Currently this is just a @(Panel) with three @(Panel:panels) inside it. To make our swipe navigation possible, we need to add three things.


- A @(Navigation) behavior
- A @(SwipeNavigate) behavior
- Entering- and ExitingAnimation to our Panels

We start off by adding a @(LinearNavigation)- and a @(SwipeNavigate) behavior.
The @(LinearNavigation) is just one of several @(Navigation types:types of navigation) that can be used.

	<Panel>
		<LinearNavigation Easing="CircularOut"/>
		<SwipeNavigate SwipeDirection="Left"/>
		<Panel Background="Red"/>
		<Panel Background="Blue"/>
		<Panel Background="Green"/>
	</Panel>

This adds all the behavior we need for navigation, but this is not enough to let us swipe between pages. The pages don't know how to animate themselves when they @(EnteringAnimation:enter) or @(ExitingAnimation:exit). For that we use @(EnteringAnimation) and @(ExitingAnimation).


	<Panel>
		<Style>
			<Panel>
				<EnteringAnimation>
					<Move X="-1" RelativeTo="ParentSize"/>
				</EnteringAnimation>
				<ExitingAnimation>
					<Move X="1" RelativeTo="ParentSize" Duration="0.5"/>
				</ExitingAnimation>
			</Panel>
		</Style>
		<LinearNavigation Easing="CircularOut" />
		<SwipeNavigate SwipeDirection="Left"/>
		<Panel Background="Blue"/>
		<Panel Background="Red"/>
	</Panel>

Now the @(Panel:panels) will move to the side when they are navigate to and from by the width of their parent container.

## $(Page)

When a @(Panel) contains a @(Navigation) behavior, all its direct children act as pages in that navigation context. Since we usually want to style our pages a bit differently than the rest of our @(Panel:panels), Fuse comes with a `Page` type which we can use instead. The `Page` type is almost no different than a @(Panel), but comes with a `Title` property for convenience.

It also lets us differentiate between pages and normal panels when styling our app.

## $(PageControl)

Since swiping between pages is such a common thing to see in apps, Fuse has a wrapper @(Control) for exactly this.

We actually already built most of the behavior of the `PageControl` from scratch in @(Basic navigation). One difference to note is that the `PageControl` styles @(Page:pages), and not @(Panel:panels), so it should be used like this:

```
<PageControl>
	<Page Background="Red"/>
	<Page Background="Blue"/>
</PageControl>
```

`PageControl` has a few properties that might be of interest:

* `Active` - The currently active page
* `CanGoBack` - `true` if it is possible to navigate backward
* `CanGoForward` - `true` if it is possible to navigate forward
* `PageCount` - Current page number
* `PageProgress` - Progress in the navigation from 0.0 to the amount of pages minus one.

You can set the active `Page` in a `PageControl` by assigning the `Active` property to the name of your `Page`. In the following example, We have a 3-page app, and a button that always returns the user to the first page.

```
<App Theme="Basic" Background="#eeeeeeff">
  <DockPanel>
    <JavaScript>
      var Observable = require("FuseJS/Observable");
      var currentPage = Observable("Page1");
      function clickHandler() {
		  currentPage.value = "Page1";
      }
      module.exports = {
		  clickHandler: clickHandler,
		  currentPage: currentPage
      };
    </JavaScript>
    <PageControl Active="{currentPage}" Dock="Fill">
      <Page Name="Page1" Background="Red"/>
      <Page Name="Page2" Background="Green"/>
      <Page Name="Page3" Background="Blue"/>
    </PageControl>
    <Button Text="Home" Clicked="{clickHandler}" Dock="Bottom"/>
  </DockPanel>
</App>
```

The [Pages using JavaScript](https://www.fusetools.com/community/examples/pageslist) example shows this in practice.

### $(PageIndicator)

The `PageIndicator` can be used together with @(PageControl) to show clearly which page is selected. The following example has four pages in a @(PageControl) and a circle docked to the bottom for each @(Page). When a @(Page) becomes `Active` its corresponding indicator is scaled by a factor of 1.3.

	<DockPanel>
		<PageControl ux:Name="pageControl">
			<Page Background="Red"/>
			<Page Background="Blue"/>
			<Page Background="Green"/>
			<Page Background="Yellow"/>
		</PageControl>
		<PageIndicator Navigation="pageControl" Dock="Bottom" Alignment="Center">
			<Circle ux:Generate="Factory" ux:Binding="DotFactory"
					Fill="Teal" Margin="5" Width="20" Height="20">
				<ActivatingAnimation>
					<Scale Factor="1.3"/>
				</ActivatingAnimation>
			</Circle>
		</PageIndicator>
	</DockPanel>

It is mandatory to set a value for the @(PageIndicator:page indicators) `Navigation` property. In this case we simply set it to be the @(PageControl).

Another thing to note is that the `PageIndicator` uses a `Factory` to create an indicator for each page. In the example above, we use a @(Circle) with the @(ux:Generate) property set to `Factory` and the @(ux:Binding) property set to the page indicator's `DotFactory` property as our "template". If nothing is set, the $(PageIndicator:page indicator) will default to using a `PageIndicatorDot`, a simple dot template that will do the trick in many cases.

## {Page} Binding


The `{Page name}` syntax allows linking with resources on the currently active navigation page. It works much like `{Resource name}`, except that it starts looking from the current page instead of the current node. A common use for this is to show a title for the current page.

In the following example, we have a @(DockPanel) with a @(Panel) and a @(PageControl). We would like the contents of our @(Panel) to display the title of the current @(Page), so we set its `Navigation` property to be the @(PageControl).

```
<DockPanel>
	<Panel Navigation="myNav" Dock="Top" Height="100" Background="Teal">
		<Text Value="{Page title}" FontSize="22" Alignment="Center"/>
	</Panel>
	<PageControl ux:Name="myNav">
		<Page Background="Red">
			<string ux:Key="title" ux:Value="First page"/>
		</Page>
		<Page Background="Green">
			<string ux:Key="title" ux:Value="Second page"/>
		</Page>
	</PageControl>
</DockPanel>
```

`Navigation="myNav"` establishes the navigation context for this node and its descendants. It can be placed on any node, and overridden on the children. Any binding, gesture, or otherwise that refers to a navigation will now refer to this one.

To show the title of the page we use the `{Page title}` binding:

```
<Text Value="{Page title}"/>
```

`{Page title}` is a resource binding that starts at the current page of the navigation (the one we specified with `Navigation="TheNav"`. Any time the current page changes this value will also change.

### Defining Title

The title of the page still needs to be defined somewhere. We do this on the pages of in our navigation using the generic resource syntax:

	<string ux:Key="Title" ux:Value="The First Page"/>

`ux:Key="Title"` defines a resource with the name `Title` and the `ux:Value` specifies the value. These are just normal resources -- anything that can use resources will also work through the `{Page}` binding.

### PageIndicator

This feature can be used to create an interesting page indicator. We can replace the dots with images for each of the pages. On the page we define the image source we want to use:

	<FileImageSource ux:Key="Image" File="icons_200/bowling.png"/>

Again, this is just a normal resource binding that happens to refer to an image source.

With these on each page we can create a `PageIndicator` that uses images for the "dots":

	<PageIndicator Navigation="TheNav" ...>
		<Image ux:Binding="DotFactory" ux:Generate="Factory" Source="{Page Image}" ...>

<!-- todo: Write about Factory -->

## $(Navigation types)

There are three navigation types, and they have quite different behaviors and use cases. Each of them inherit from the `Navigation` base type, and therefore share a few properties:

* `Active` - contains the @(Node) which is currently navigated to.
* `CanGoBack` - is `true` if you can navigate back.
* `CanGoForward` - is `true` if you can navigate forward.
* `PageCount` -  contains the number of pages the `Navigation` can navigate between
* `PageProgress` - contains how far the `Navigation` has navigated, from 0.0 to `PageCount` - 1

### $(LinearNavigation)

`LinearNavigation` is used when each page should be laid out linearly. So with a swipe navigation, one would start from page 1, swipe to page 2, then page 3 and so on. Navigating directly to page 3 from page 1 would cause a quick visit to page 2 on the way.

### $(DirectNavigation)

With `DirectNavigation` there is no implicit flow between pages. Any @(Page) can be directly navigated to from any other @(Page). If we have 5 pages in our @(Navigation), there would be no scrolling over the other pages when navigating from page 1 to page 5 as there would be with a @(LinearNavigation).

### $(HierarchicalNavigation)

A `HierarchicalNavigation` is commonly used when there is a hierarchical flow of pages. This is commonly found in the settings apps on iOS and Android devices. One first picks a topic, then a subtopic and so on, taking us deeper into the hierarchy of options. Navigating in this context means pushing a page onto a stack of pages. For each navigation there is a natural back navigation which takes us back the the page navigated from.

> ### HierarchicalNavigation example

This is an example that demonstrates usage of `HierarchicalNavigation` in conjunction with @(NavigateTo), @(GoBack), @(GoForward), and @(WhileCanGoForward).

```
<Panel>
	<HierarchicalNavigation ux:Name="nav" ReuseExistingNode="false" Active="mainPage" />

	<Style>
		<Page>
			<EnteringAnimation>
				<Move X="1" RelativeTo="ParentSize" />
			</EnteringAnimation>
			<ExitingAnimation>
				<Move X="-1" RelativeTo="ParentSize" />
			</ExitingAnimation>
		</Page>
	</Style>

	<Page ux:Name="mainPage">
		<StackPanel>
			<Button Text="Page 1">
				<Clicked>
					<NavigateTo Target="subPage1" />
				</Clicked>
			</Button>
			<Button Text="Page 2">
				<Clicked>
					<NavigateTo Target="subPage2" />
				</Clicked>
			</Button>
			<WhileCanGoForward>
				<Button Text="Go Forward">
					<Clicked>
						<GoForward />
					</Clicked>
				</Button>
			</WhileCanGoForward>
		</StackPanel>
	</Page>

	<Page ux:Name="subPage1">
		<StackPanel>
			<Text>Welcome to page 1!</Text>
			<Button Text="Go Back">
				<Clicked>
					<GoBack />
				</Clicked>
			</Button>
		</StackPanel>
	</Page>

	<Page ux:Name="subPage2">
		<StackPanel>
			<Text>Welcome to page 2!</Text>
			<Button Text="Go Back">
				<Clicked>
					<GoBack />
				</Clicked>
			</Button>
		</StackPanel>
	</Page>
</Panel>
```

> ## $(EdgeNavigator)

`EdgeNavigator` is designed to allow you to dock content on the edge of a @(Panel), typically used in full screen.

	<EdgeNavigator>
		<Panel Width="150" EdgeNavigation.Edge="Left" Background="#f63" />
		<Panel Background="#fff>
		<Text Alignment="Center">
			This is an example of EdgeNavigator!
		</Text>
		</Panel>
	</EdgeNavigator>

Note that setting the @(Background) of the second @(Panel) in this example is of significance, as it enables hit testing of the inner @(Panel). You could alternatively set the @(HitTestMode) of the @(EdgeNavigator) to be @(HitTestMode.LocalBoundsAndChildren). Normally this isn't an issue, as the inner panel will have content that is hit testable.

## $(Controlling navigation)

Fuse provides several @(Actions:actions) that allow you to perform @(Navigation:navigation).

All navigation-related @(Actions:actions) have a `Target` property that lets you specify the navigation context to perform the action on.
If `Target` is not specified, it will look for a parent element with a `Navigation` behavior and use that.

### GoBack

The behavior of `GoBack` depends on the type of navigation context (`Target`) it's performed on:

- @(LinearNavigation) – Navigates to the page occurring before the current page.
- @(HierarchicalNavigation) – Navigates one level up in the hierarchy, i.e. the page most recently navigated to.
- @(DirectNavigation) – Does nothing.

> ### $(WhileCanGoBack)

The `WhileCanGoBack` trigger is active whenever navigating backward is possible.

### GoForward

As with @(GoBack), `GoForward` is also context-sensitive:

- @(LinearNavigation) – Navigates to the page occurring after the current page.
- @(HierarchicalNavigation) – Navigates one level down in the hierarchy, i.e. the last page the user has @(GoBack:gone back) from.
- @(DirectNavigation) – Does nothing.


> ### $(WhileCanGoForward)

The `WhileCanGoForward` trigger is active whenever navigating forward is possible.


### $(NavigateTo)

`NavigateTo` navigates to a specific @(Page:page), specified by the `Target` property. Below is an example using a @(PageControl).

	<PageControl ux:Name="nav">
		<Page ux:Name="page1">
			<Button Text="Go to page 2" Alignment="Center">
				<Clicked>
					<NavigateTo Target="page2" />
				</Clicked>
			</Button>
		</Page>
		<Page ux:Name="page2">
			<Text Alignment="Center">
				Welcome to page 2!
			</Text>
		</Page>
	</PageControl>


## $(EnteringAnimation) / $(ExitingAnimation)

As seen above, Entering- and ExitingAnimation are used to specify how pages and elements behave when they are being navigated to and from. There is no default behavior for elements when using `Navigation` so unless a @(PageControl) is being used (which does apply a style with Entering- and ExitingAnimation to @(Page:pages)) one has to add these in order to define what a navigation actually means for a page.

@(PageControl) applies the following style to @(Page:pages):

	<Style>
		<Page>
			<EnteringAnimation>
				<Move X="-1" RelativeTo="ParentSize" Duration="0.5"/>
			</EnteringAnimation>
			<ExitingAnimation>
				<Move X="1" RelativeTo="ParentSize" Duration="0.5"/>
			</ExitingAnimation>
		</Page>
	</Style>

It just moves each page by the width of its parent, which is a common style for @(LinearNavigation) situations.

There are of course no limits to what can be animated while navigating. The following style also @(Scale:scales) the pages as they enter/exit.

	<Style>
		<Page>
			<EnteringAnimation>
				<Move X="-1" RelativeTo="ParentSize" Duration="0.5"/>
				<Scale Factor="0.2" Duration="0.5"/>
			</EnteringAnimation>
			<ExitingAnimation>
				<Move X="1" RelativeTo="ParentSize" Duration="0.5"/>
				<Scale Factor="0.6" Duration="0.5"/>
			</ExitingAnimation>
		</Page>
	</Style>

## $(ActivatingAnimation)

`ActivatingAnimation` is triggered as at page is navigated to and from (when it is becoming the active page). As a @(Page) is navigated to, any `ActivatingAnimation` is triggered. The animation progresses from 0 as the page is entering and becomes 1 when the page is done entering. As the page exits `ActivatingAnimation` progresses from 1 back to 0.

Here is a more advanced use of @(EnteringAnimation:Entering-), @(ExitingAnimation:Exiting-) and @(ActivatingAnimation):

    <Style>
		<Page ux:Name="page">
			<EnteringAnimation>
				<Move RelativeTo="ParentSize">
					<Keyframe X="-0.7" Time="0.5"/>
					<Keyframe X="0" Time="1"/>
				</Move>
				<Rotate>
					<Keyframe DegreesZ="20" Time="0.5"/>
					<Keyframe DegreesZ="0" Time="1"/>
				</Rotate>
				<Change page.Opacity="0" Duration="1"/>
				<Scale Factor=".5" Duration="1"/>
			</EnteringAnimation>
			<ExitingAnimation>
				<Move RelativeTo="ParentSize">
					<Keyframe X="0.7" Time="0.5"/>
					<Keyframe X="0" Time="1"/>
				</Move>
				<Rotate>
					<Keyframe DegreesZ="-20" Time="0.5"/>
					<Keyframe DegreesZ="0" Time="1"/>
				</Rotate>
				<Scale Factor=".5" Duration="1"/>
			</ExitingAnimation>
			<WhileActive Threshold="0.5">
				<BringToFront/>
			</WhileActive>
		</Page>
	</Style>

<!-- TODO: Document whats going on? -->

## $(WhileActive)

`WhileActive` animates as a page is becoming active.

## $(WhileInactive)

`WhileInactive` animates as a page is becoming inactive.

<!--
## $(WhileInEnterState)
TODO
## $(WhileInExitState)
TODO
## $(WhileNavigating)
TODO -->

## $(SwipeNavigate)

`SwipeNavigate` is used to connect swiping gestures to @(Navigation).
`SwipeNavigate` has three properties which modify its behavior: @(SwipeDirection), @(SwipeEnds) and @(VelocityThreshold).

### $(SwipeDirection)

Can have the values `Down`, `Right`, `Left` and `Up`. These values indicate which direction one swipes in order to navigate to the next page.

### $(SwipeEnds)

Can have the values `Closed`, `Open` and `Short`.

`Closed` means that when one reaches the first or last page, one cannot swipe further.
`Open` means that when one reaches the first or last page, one can swipe further as much as is possible. This does not mean that its possible to navigate to an undefined @(Page) however.
`Short` acts similarly to `Open` except that one can only swipe a a short distance beyond the first or last page.

### $(VelocityThreshold)

`VelocityThreshold` is used to decide how fast one has to swipe a page in order for it to be interpreted as a navigation. This means that one does not have to swipe the whole page away, but can get away with a short but fast flick gesture.


## Databind active page

It is quite possible to perform navigation through data-binding.
To do this, we data-bind the navigations `Active` property to a string in JavaScript. This string should correspond to the Name of the @(Page) we intend to navigate to.

<!-- TODO: Create example where both pages and direct navigation is databound. (perhaps hierarchical?) -->
