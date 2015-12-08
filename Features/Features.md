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
			<b>Layout Controls</b>
			<p>Define flexible, responsive layout for your app screens.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>Shapes</b>
			<p>Build UI designs with fast vector shapes to easily adapt your design to any screen size and density.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>Image</b>
			<p>Display images and icons with multi-density support to adapt to any screen.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>Video</b>
			<p>Play videos, either as a media player or as part of your UI design.<p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__planned">Planned</td>
		<td class="feature__planned">Planned</td>
	</tr>
	<tr>
		<td>@(Navigation)</td>
		<p>General purpose, cross-platform navigation system, which lets you add custom gesture-driven anmiated navigation to any part of your app.</p>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>TextEdit</b>
			<p>A plain, unthemed text input field.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>TextInput</b>
			<p>A text input field decorated with look and feel from the Theme.</p>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>Button</b>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>Slider</b>
		</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
		<td class="feature__mature">Mature</td>
	</tr>
	<tr>
		<td>
			<b>Switch</b>
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
			<b>WebView</b>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__experimental">Experimental</td>
	</tr>
	<tr>
		<td>
			<b>MapView</b>
		</td>
		<td class="feature__experimental">Experimental</td>
		<td class="feature__planned">Planned</td>
	</tr>
	<tr>
		<td>
			<b>DatePicker</b>
		</td>
		<td class="feature__planned">Planned</td>
		<td class="feature__planned">Planned</td>
	</tr>

	<tr>
		<td>
			<b>iOS Navigation</b>
		</td>
		<td class="feature__n-a">N/A</td>
		<td class="feature__planned">Planned</td>
	</tr>

	<tr>
		<td>
			<b>Android Material Design Navigation</b>
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
			<b>Push Notifications</b>
		</td>
		<td class="feature__n-a">N/A</td>
		<td class="feature__planned">Planned</td>
		<td class="feature__n-a">N/A</td>
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