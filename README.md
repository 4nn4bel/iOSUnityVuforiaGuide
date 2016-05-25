# iOSUnityVuforiaGuide
Integration of Unity 5.3 + Vuforia 5.5.9 project with native iOS application (Xcode 7.3)

Inspired by [the-nerd.be/](https://the-nerd.be/) tutorials, by Frederik Jacques.

Let's assume that you already have a Unity project which uses Vuforia SDK.

In project's Player Settings be sure that you use:
* Scripting Backend - IL2CPP
* Uncheck 'Auto Graphics API' and use only OpenGLES2, because for iOS 9+ Unity tries to use Metal and you can face some issues with that (in my case there were no texture on 3d object)

Add scene to build and generate iOS project from Unity project and after this step you can close Unity.

##Step 1
Create Xcode project and add `UnityIntegration.xcconfig` which you can find in this repo.

As soon as we need to use files from previously generated iOS project, you can put the projects together in a common directory.

Select project settings and choose that file to be used by your project:

![](imgs/1_1.png?raw=true "")

Now choose `Target -> Build Settings` scroll down to the end of the list and change values of 
`UNITY_IOS_EXPORTED_PATH` and `UNITY_RUNTIME_VERSION` to path of Unity-generated iOS project and version of Unity you use accordingly.

![](imgs/1_2.png?raw=true "")

##Step 2
Remove `Main.storyboard` from Xcode project.
Also, in `Info.plist` remove `Main storyboard file base name` row:

![](imgs/2.png?raw=true "")

##Step 3
Create new group, let's call it `"Integration"`.

Drag and drop inside this group `Classes` and `Libraries` folders from previously Unity-generated iOS project.

Make sure, that:
* `Copy resources if needed` is <strong>unchecked</strong>
* `Create groups` is <strong>checked</strong>

This operation could take few minutes as soon as there's a lot of files inside the folders.

![](imgs/3.png?raw=true "")

##Step 4

Now lets's make some cleanup, so Xcode won't be lagging while processing a lot of not neeed files.

###Step 4.1
Inside `Classes` folder choose `Native` folder and search for `.h` files on the bottom of Navigator.

We need to remove references to `.h` files inside `Native` folder, except those which starts with `"Vuforia"` (they're at the end of the list). I'd recommend not to select and remove all files at once, because Xcode can stuck or even crash :( 

So when pressing `Delete`, make sure to press <em>`Remove References`</em>:

![](imgs/4_1.png?raw=true "")

###Step 4.2

Go to `Libraries` folder and remove reference for `libil2cpp` folder.

Again, make sure to press <em>`Remove References`</em>:

![](imgs/4_2.png?raw=true "")

##Step 5
Also we need to add some more files from previously Unity-generated iOS project.

Drag and drop inside the `Integration` group `Data` folder.

Check if:
* `Copy resources if needed` is <strong>unchecked</strong>
*  But this time make sure to <strong>check</strong> <em>`Create folder references`</em>

![](imgs/5.png?raw=true "")


##Step 6

Add frameworks to the project, so the list will look like this:

![](imgs/6.png?raw=true "")

##Step 7

Create `PrefixHeader.pch` file: `File -> New -> Other -> PCH file`

Remove all code inside and insert the following: 

```
#ifndef PrefixHeader_pch
#define PrefixHeader_pch

#ifdef __OBJC__
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
#endif

#include "Preprocessor.h"
#include "UnityTrampolineConfigure.h"
#include "UnityInterface.h"

#ifndef __OBJC__
#if USE_IL2CPP_PCH
#include "il2cpp_precompiled_header.h"
#endif
#endif

#ifndef TARGET_IPHONE_SIMULATOR
#define TARGET_IPHONE_SIMULATOR 0
#endif

#define printf_console printf

#endif
```

Now in the `Build Settings` under `Apple LLVM 7.1 - Language` section, find field called `Prefix Header` and instead of `/ENTER/PATH/HERE` add path to previously created file `YOUR_PROJECT_NAME/PrefixHeader.pch` which in my case is:  `UnityIntegration/PrefixHeader.pch`

![](imgs/7.png?raw=true "")

##Step 8

Rename `main.m` to `main.mm` and `AppDelegate.m` to `AppDelegate.mm`

![](imgs/8.png?raw=true "")

##Step 9


