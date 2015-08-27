### Problem overview

Recently I decided to try [SwiftKey keyboard for Android](https://play.google.com/store/apps/details?id=com.touchtype.swiftkey) and found that it's pretty cool. My major finding was presence of alternative layouts for the `Ukrainian` and `Russian` languages. In contrast to the `English`, these languages contain more characters in their alphabets, so more keys per row are present. Because of that buttons are very small and it's hard to hit them. In alternative layouts some chars become hidden, making typing much comfortable.

Unfortunately, after I switched to that alternative layouts, I noticed that each time my device is rebooted, layout preferences are being reset. Another bug was that it was unable to load language pack for `English` after reboot, so I had to re-download it. I'm using CyanogenMod 10.1.3 on my Samsung Galaxy S2 and it hangs very often, so it could happen that my device is being rebooted for a couple of times per day. You might imagine how annoying the SwiftKey bug was for me ― each time I had to navigate deeply inside the settings to re-download language pack and switch layouts to alternative. 

I tried to Google over the Internet and found that other guys observe the similar issues:
- <https://vip.swiftkey.com/index.php?/topic/11731-when-i-restart-my-phone-layout-reset-to-the-default/>
- <https://support.swiftkey.com/hc/en-us/articles/201458342-Why-am-I-losing-my-language-pack-after-reboot-> (that happened for `English` actually)

I tried to apply different suggestions including backup disabling, reset and files removals however none of them helped. One day I decided to figure out how I could prevent that.

### How to fix

In order to apply these steps you need to have __rooted device__. Also steps are spitted into 2 parts ― one time actions and actions you need to apply after each update installation.

#### Tested on: 
__Model:__ Samsung Galaxy S2  
__Firmware:__ CyanogenMod 10.1.3 (Android 4.2.2)  
__SwiftKey Keyboard version:__ 5.3.4.67


#### One time steps 
You need to apply these steps for one time only, they will survive the update installations.

1\. Modify the `/data/data/com.touchtype.swiftkey/shared_prefs/com.touchtype.swiftkey_preferences.xml` file and add the following line to the end of file:
```
<boolean name="store_static_model_on_internal_storage" value="true" />
```
Ensure that you insert it __before__ the closing `</map>` tag.

Be aware of [Permissions Fix](#permissions-fix)

2\. Alter the `/data/data/com.touchtype.swiftkey/files/language_models/downloadedLanguagePacks.json` file and set its content to the following:

```
{}
```

Be aware of [Permissions Fix](#permissions-fix)

3\. Restart SwiftKey keyboard (e.g. reboot device or kill app) and re-download languages in configuration dialog.

4\. Apply the _Each update steps_ below.


#### Each update steps
I'd suggest to turn off automatic update for SwiftKey Keyboard app in Google Play Store. Otherwise, you might skip update installation and settings will be reset on next reboot.

The simplest way to apply changes will be to install the [Lucky Patcher](http://lucky-patcher.netbew.com/) app. It allows to patch Dalvik cache files automatically.


__Lucky Patcher steps:__

1. Open Lucky Patcher and find the SwiftKey Keyboard app. Select it to open menu.
1. Select `Open Menu of Patches` option.
1. Select `Manual Patcher` option.
1. In the appeared dialog enter the following values:  
Original Hex-String: `F2 21 08 00 39 01 11 00 71 00 ?? ?? 00 00 0A 01 38 01 0B 00`  
Replacement Hex-String: `?? ?? ?? ?? ?? ?? ?? ?? 00 00 00 00 00 00 00 00 00 00 00 00`
1. Press the `Patch` button and wait for the end. If patch was successful, you will get green message.


__Manual patch steps:__

1. Be aware of [Permissions Fix](#permissions-fix)
1. Navigate to the `/data/dalvik-cache` directory.
1. Find the file named `data@app@com.touchtype.swiftkey-XXX.apk@classes.dex`, where `XXX` ― any number (e.g. `data@app@com.touchtype.swiftkey-2.apk@classes.dex`).
1. Open file in any hex editor (e.g. [HxD](http://mh-nexus.de/en/hxd/)).
1. Find the following bytes sequence: `F2 21 08 00 39 01 11 00 71 00 ?? ?? 00 00 0A 01 38 01 0B 00`, where `??` ― byte with any value. (HxD doesn't support mask search, therefore search for bytes preceding `??` and compare rest bytes manually later).
1. Replace bytes above with the following bytes: `F2 21 08 00 39 01 11 00 00 00 00 00 00 00 00 00 00 00 00 00` 
1. Save changes. Replace original file with the modified one. 



### Permissions Fix

In order to modify file you might need to copy it to other directory (e.g. SD Card), modify it there and later copy it back to its original location. In such case you need to fix permissions after you put file back. 
 
__Steps to follow__:  

1. Install any app that allows to modify permissions (e.g. [ES File Explorer File Manager](https://play.google.com/store/apps/details?id=com.estrongs.android.pop)).
1. Copy file to custom location and modify it there.
1. Navigate to the original file and remember its permissions before replacing it (e.g. Properties dialog for ES FEFM). More specifically, remember `Owner name`, `Group name` and `Permissions`.
1. Copy altered file to its original location.
1. Restore permissions remembered at the point 2 (e.g. Properties dialog for ES FEFM).
