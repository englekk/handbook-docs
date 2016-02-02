# $(Uno Project Format Overview)

The Uno Project Format (`.unoproj`) describes what files and packages to include in a project, as well as settings for how the
app should be built when exported.

Uno Project files can be edited through the Fuse desktop tools, by the `fuse` command line tool, or by editing the project file by hand
in a text editor.

## Structure

Here is an example Uno project file with with all possible fields:

```
{
    "BuildDirectory": "build",
    "CacheDirectory": ".uno",
    "OutputDirectory": "\$(BuildDirectory)/@(Target)/@(Configuration)",
    "RootNamespace": "\$(QIdentifier)",
    "ReferenceUnoCore": true,
    "Version": "0.0.0",
    "VersionCount": 1,
    "Title": "\$(Name)",
    "Copyright": "Copyright (C) 2015",
    "Publisher": "[Publisher]",
    "Mobile": {
        "KeepAlive": false,
        "ShowStatusbar": true,
        "RunsInBackground": true,
        "Orientations": "Auto"
    },
    "Android": {
        "ApplicationLabel": "\$(Title)",
        "VersionCode": "\$(VersionCount)",
        "VersionName": "\$(Version)",
        "Description": "\$(Description)",
        "Icons": {
			"LDPI": "\$(Icon)",
			"MDPI": "\$(Icon)",
			"HDPI": "\$(Icon)",
			"XHDPI": "\$(Icon)",
			"XXHDPI": "\$(Icon)",
			"XXXHDPI": "\$(Icon)"
        },
		"NDK": {
			"PlatformVersion": 9
		},
		"SDK": {
			"BuildToolsVersion": "23.0.0",
			"CompileVersion": 19,
			"MinVersion": 10,
			"TargetVersion": 19
		}
    },
    "iOS": {
        "Icons": {
			"iPhone_29_2x": "\$(Icon)",
			"iPhone_29_3x": "\$(Icon)",
			"iPhone_40_2x": "\$(Icon)",
			"iPhone_40_3x": "\$(Icon)",
			"iPhone_60_2x": "\$(Icon)",
			"iPhone_60_3x": "\$(Icon)",
			"iPad_29_1x": "\$(Icon)",
			"iPad_29_2x": "\$(Icon)",
			"iPad_40_1x": "\$(Icon)",
			"iPad_40_2x": "\$(Icon)",
			"iPad_76_1x": "\$(Icon)",
			"iPad_76_2x": "\$(Icon)"
        },
		"BundleIdentifier": "com.uno.\$(Name)",
		"BundleName": "\$(Title)",
		"DeploymentTarget": "7.0",
		"PList": {
			"MKDirectionsApplicationSupportedModes": [
				"MKDirectionsModeCar",
				"MKDirectionsModeBus",
			],
			"UIRequiresPersistentWiFi": true,
			"UIRequiredDeviceCapabilities": [
				"camera-flash"
			]
		},
    },
    "HTML": {
        "Title": "\$(Title)",
        "Favicon": "\$(Icon)"
    },
    "InternalsVisibleTo": [],
    "Packages": [
		"Fuse.Animations",
    	"Fuse.BasicTheme",
    	"Fuse.Themes",
    	"Fuse.Controls",
    	"Fuse.Designer",
    	"Fuse.Drawing",
    	"Fuse.Drawing.Primitives",
    	"Fuse.Effects",
		"Fuse.Scripting",
    	"Fuse.Elements",
    	"Fuse.Entities",
    	"Fuse.Gestures",
    	"Fuse.Navigation",
    	"Fuse.Shapes",
    	"Fuse.Triggers",
    	"Fuse.Reactive",
    	"Fuse.Android",
    	"Fuse.Desktop",
    	"Fuse.iOS",
    	"FuseCore",
    	"Uno.Collections",
    	"Uno.Geometry"
    ],
    "Projects": [],
    "Includes": [
        "*"
    ],
    "Excludes": []
}
```

* Note: Setting null for android NDK and SDK options results in the following default values being used:

```
"Android": {
    "NDK": {
      "PlatformVersion": null     // default: 9
    },
    "SDK": {
      "BuildToolsVersion": null,  // default: 23.0.0
      "CompileVersion": null,     // default: 19
      "MinVersion": null,         // default: 10
      "TargetVersion": null       // default: 19
    }
}
```

## Globs

These glob features are supported in `Includes` and `Excludes` properties:

- Brace expansion
- Extended glob matching
- Globstar matching

Patterns without `/` in `.unoproj` or `.unoignore` files are matched recursively.

    *             # Matches all files.
    *.foo         # Mathces all files named *.foo.
    *.bar         # Matches all files named *.bar.
    *.+(bar|foo)  # Matches all files named *.bar or *.foo .
    foobar        # Matches all files named foobar.

Add `/` or `./` to disable recursion.

    /*.png        # Not recursive, matches all PNG files found directly in project directory.

Use globstar (`**`) for explicit recursion.

    Foo/**/*.uno  # Matches all files named *.uno in Foo directory recursively.

## $(Icons)

To set the default icon, use the `Icon` property (not the `Includes` property):

```
"Icon": "icon-default.png",
"Includes": [
	...
]
```

If you explicitly want to add specific resolutions, you can override specialized properties:

```
"Android": {
  "Icons": {
    "LDPI": "icon-ldpi.png",
    "MDPI": "icon-mdpi.png",
    "HDPI": "icon-hdpi.png",
   }
}
```

## $(iOS launch images)

To set defaul launch images on iOS, you use the `LaunchImages` property, like so:

```
  "iOS": {
    "LaunchImages": {
      "iPhone_Portrait_2x": "...",   // 640x960
      "iPhone_Portrait_R4": "...",   // 640x1136
      "iPhone_Portrait_R47": "...",  // 750x1334
      "iPhone_Portrait_R55": "...",  // 1242x2208
      "iPhone_Landscape_R55": "...", // 2208x1242
      "iPad_Portrait_1x": "...",     // 768x1024
      "iPad_Portrait_2x": "...",     // 1536x2048
      "iPad_Landscape_1x": "...",    // 1024x768
      "iPad_Landscape_2x": "..."     // 2048x1536
    }
  }
```

## $(Signing for Android)

A Uno Project set up for signing can look like this:

    "Android": {
      "Key": {
        "Alias": "application",
        "AliasPassword": "android",
        "Store": "release.keystore",
        "StorePassword": "android"
      }
    }

A file named `release.keystore` is expected to be found in the same folder as the project.

This file can be created using the command (`keytool` is found in Android SDK.):

    keytool -genkey -v -keystore release.keystore \
        -alias application -keyalg RSA -keysize 2048 -validity 10000

Note that only release builds are signed using the specified key. Debug builds are automatically signed using a debug key. To do a release build, use `uno build --target=android --configuration=Release`.

## $(Signing for iOS)

Run `uno build --target=iOS --configuration=Release -adebug` from the Terminal/command line and then [follow the regular procedure for adding your signing certificate](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW1) and then [submitting your app](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/SubmittingTheApp.html#//apple_ref/doc/uid/TP40011225-CH33) to the Apple App Store.
