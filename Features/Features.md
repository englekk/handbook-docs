<documentProperties pageTitle="Features" />

# Feature Dashboard

Keep track of your favorite features on Fuse's journey through Beta.

This Dashboard is updated for each release.

## Legend

<table>
	<tr>
		<td class="feature__mature">Mature</td>
		<td>The feature is done, tested, documented and can be used generally in production apps. If you run into problems with features marked as Mature, the Fuse team will deal with bug reports quickly. </td>
	</tr>
	<tr>
		<td class="feature__experimental">Experimental</td>
		<td>Feature is shipped in some form, but may be incomplete. Documentation is limited and often only available a particular group of testers. The API or behavior is likely to change in the future based on feedback from testing. If you are using an experimental feature, make sure to check the changelog for updates when upgrading Fuse. </td>
	</tr>
	<tr>
		<td class="feature__planned">Planned</td>
		<td>The feature is currently being worked on, or we plan to start working on it quite soon.</td>
	</tr>
	<tr>
		<td class="feature__backlog">Backlog</td>
		<td>The feature is on our todo-list for the future, but implementation has not yet been planned.</td>
	</tr>
	<tr>
		<td class="feature__n-a">N/A</td>
		<td>The feature is not possible or desirable to implement on the target platform or configuration.</td>
	</tr>
</table>


## Basic UX Components

Basic UX components are the fundamental building blocks for creating cross-platform app screens. These components work
across all themes, in both `Native` mode and `Graphics` mode. Appearance of certain controls (such as Button) may vary in different
themes.

<table>
	<tr>
		<td></td>
		<td><b>Graphics Mode</b></td>
		<td><b>Native iOS</b></td>
		<td><b>Native Android</b></td>
	</tr>
	<tr>
		<td>
			@(Layout)
			<p>Define flexible, responsive layout for your app screens.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(Shapes)
			<p>Build UI designs with fast vector shapes to easily adapt your design to any screen size and density.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(Image)
			<p>Display images and icons with multi-density support to adapt to any screen.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(Video)
			<p>Play videos, either as a media player or as part of your UI design.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__planned">Planned</td>
		<td class="feature__planned">Planned</td>
	</tr>
	<tr>
		<td>
			@(Navigation)
			<p>General purpose, cross-platform navigation system, which lets you add custom gesture-driven anmiated navigation to any part of your app.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(TextEdit)
			<p>A plain, unthemed text input field.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(TextInput)
			<p>A text input field decorated with look and feel from the Theme.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(Button)
			<p>Basic clickable control.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(Slider)
			<p>Lets you specify a numeric value with a track and knob</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			@(Switch)
			<p>Lets you specify an on/off boolean value.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>

</table>


## Advanced UX Components

These more advanced native components are available in fuse through `Native` theme, or inside a `NativeViewHost`. They are
not available in `Graphics` theme, and can not interact with the Fuse effect system.

Their features, function and appearence may vary between platforms.

<table>
	<tr>
		<td>
			@(WebView)
			<p>Display a web browser in your app, or create parts of your app using HTML5.</p>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__experimental">Experimental</td>
	</tr>
	<tr>
		<td>
			@(MapView)
			<p>Display the OS' native maps</p>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__planned">Planned</td>
	</tr>
	<tr>
		<td>
			@(DatePicker)
			<p>Display the OS' native date picker</p>
		</td>
		<td class="feature__planned">Planned</td>
		<td class="feature__planned">Planned</td>
	</tr>

	<tr>
		<td>
			<u>iOS Navigation</u>
			<p>Create a native navigation system based on iOS' built-in navigator controls and native transitions.</p>
		</td>
		<td class="feature__n-a">N/A</td>
		<td class="feature__planned">Planned</td>
	</tr>

	<tr>
		<td>
			<b>Android Material Design Navigation</b>
			<p>Create a native navigation system based on the Material Design compoent pack provided by Google.</p>
		</td>
		<td class="feature__n-a">N/A</td>
		<td class="feature__planned">Planned</td>
	</tr>
</table>


## FuseJS Features

<table>
	<tr>
		<td></td>
		<td><b>Native iOS</b></td>
		<td><b>Native Android</b></td>
	</tr>

	<tr>
		<td>
			<b>XMLHttpRequest</b>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>

	<tr>
		<td>
			<b>fetch (Promise-based HTTP)</b>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>

	<tr>
		<td>
			<b>Push Notifications</b>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__experimental">Experimental</td>
	</tr>

	<tr>
		<td>
			<b>System Notifications</b>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__experimental">Experimental</td>
	</tr>

	<tr>
		<td>
			<b>Camera (take photo)</b>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__experimental">Experimental</td>
	</tr>

	<tr>
		<td>
			<b>Geolocation</b>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__experimental">Experimental</td>
	</tr>

	<tr>
		<td>
			<b>Local Storage</b>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__experimental">Experimental</td>
	</tr>
</table>

## Native Interop

Fuse has several ways in which you can interop with native APIs, existing native code and third-party SDKs.

<table>
	<tr>
		<td></td>
		<td><b>iOS</b></td>
		<td><b>Android</b></td>
	</tr>

	<tr>
		<td>
			<b>Foreign Code</b>
			<p>Put native Java, Objective-C and C++ code directly in your Fuse project, and expose new functionality to FuseJS.</p>
		</td>
		<td class="feature__planned">In progress (0.10)</td>
		<td class="feature__planned">In progress (0.10)</td>
	</tr>

	<tr>
		<td>
			<b>Uno Inline Foreign Code</b>
			<p>Put native Java, Objective-C and C++ code directly in your Fuse project, and expose new functionality to FuseJS.</p>
		</td>
		<td class="feature__planned">In progress (0.10)</td>
		<td class="feature__planned">In progress (0.10)</td>
	</tr>

	<tr>
		<td>
			<b>Uno Native API Bindings</b>
			<p>Call any Android and iOS API directly from Uno in plain syntax.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>

	<tr>
		<td>
			<b>Uno Extension Layer (UXL)</b>
			<p>Various low-level methods of advanced interop between Uno and the native target language.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
</table>


## Real-time Graphics Effects

Real-time graphics effects takes advantage of Fuse’s powerful Graphics Mode to apply artistic GPU effects to UI elements in real time. This gives designers a new realm of new possibilities to shape visual expressions, animations and transitions. 



<table>
	<tr>
		<td></td>
		<td><b>Graphics Mode</b></td>
	</tr>

	<tr>
		<td>
			<b>Blur</b>
			<p>Apply real-time blur to any UI element.</p>
		</td>
		<td class="feature__mature">Mature</td>
	</tr>

	

	<tr>
		<td>
			<b>DropShadow</b>
			<p>Put real-time soft drop shadows on any UI element.</p>
		</td>
		<td class="feature__mature">Mature</td>
	</tr>

	<tr>
		<td>
			<b>Desaturate</b>
			<p>Put real-time soft drop shadows on any UI element.</p>
		</td>
		<td class="feature__mature">Mature</td>
	</tr>

	<tr>
		<td>
			<b>HalfTone</b>
			<p>Applies a configurable halftone pattern to any UI element.</p>
		</td>
		<td class="feature__mature">Experimental</td>
	</tr>

	<tr>
		<td>
			<b>Glass</b>
			<p>Apply real-time blur and color grading to the background of any UI element.</p>
		</td>
		<td class="feature__planned">Planned</td>
	</tr>

	<tr>
		<td>
			<b>ColorCorrect</b>
			<p>Apply artistic color correction to any UI element in real time.</p>
		</td>
		<td class="feature__planned">Planned</td>
	</tr>
</table>


> Note that GPU effects have a performance cost, and not all combinations and parameters. Applied and configured with care, all effects can be done in smooth 60 FPS even on low end devices. It is not recommended to use effects for essential features in your app, as you may need to disable them on weak devices.

The Fuse effect system is completely general, and you can easily create custom effects with Uno code. Fork one of the existing effects and or extend the Fuse.Effects class to create your own GPU shader effects. 

<table>
	<tr>
		<td>
			<b>Custom Shader Effects</b>
		</td>
		<td class="feature__mature">Mature</td>
	</tr>
</table>

## Fuse Tools

<table>
	<tr>
		<td></td>
		<td><b>Status</b></td>
	</tr>

	<tr>
		<td>
			<b>Fuse CLI Tools</b>
			<p>The `fuse` command for creating, building and previewing Fuse projects</p>
		</td>
		<td class="feature__mature">Mature</td>
	</tr>

	<tr>
		<td>
			<b>Sketch Importer</b>
			<p>The `fuse import` command automatically creates and updates UX classes and image resources from a .sketch file</p>
		</td>
		<td class="feature__experimental">Experimental</td>
	</tr>
</table>