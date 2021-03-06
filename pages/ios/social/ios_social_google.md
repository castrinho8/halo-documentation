---
title: Google Integration
keywords: ios, social, google
last_updated: December 26, 2016
tags: [social, auth]
sidebar: ios_sidebar
permalink: ios_social_google.html
folder: ios
---

This getting started guide will guide you on setting up Google Sign-In SDK for Android in a few minutes. We will provide a step by step guide to get everything working with the most basic setup.

### Step 1: Create the app

Register in the firebase console and create a new app, if you don’t already have one. If you already have an existing Google project associated with your mobile app, click Import Google Project. Otherwise, click Create New Project.

### Step 2: Generate an oAuth web key

Add the package name and and follow the setup steps.

#### Optional Step to add security

{% include note.html content="You can add extra security if you add the google id into the Halo CMS." %}

![Configure Google project](./images/google-console.png)

This step is optional and if you add this field to the HALO CMS it would verify that the tokens you provide belongs to the Google application. You can provide as many ids as you need for different platforms. In another case the HALO system only verifies if it is a valid token against Google.

You can add this information in app section on HALO CMS.

![Add extra security to facebook tokens](./images/halo-cms-secure-social.png)

### Step 3: Generate the hashes (Optional)

To generate a hash of your release key, run the following command substituting your release key alias and the path to your keystore.

```
keytool -exportcert -alias <RELEASE_KEY_ALIAS> -keystore <RELEASE_KEY_PATH> | openssl sha1 -binary | openssl base64
```
This command should generate a string. Copy and paste this Release Key Hash into your firebase console.

### Step 4: Download and setup configuration file

At the end, you'll download a GoogleService-Info.plist. You can download this file again at any time. Copy this file into your project's files.

### Step 5: Configure the project

Add the needed libraries from Firebase: **FirebaseAnalytics.framework**, **FirebaseCore.framework**, **FirebaseInstanceID.framework**, **GoogleInterchangeUtilities.framework**, **GoogleSymbolUtilities.framework**, **GoogleUtilities.framework**, **FirebaseAuth.framework**, **GoogleNetworkingUtilities.framework** and **GoogleSignIn.framework**.

{% include warning.html content="**GoogleSignIn.framework** is inside the **Invites** folder when you download the Firebase SDK. You need this framework to compile your project." %}

Remove all the libraries from **Linked Frameworks and Libraries** and **Embedded Binaries**.

Add the *-ObjC* flag to **Other Linker Settings**. This can be found inside **Build Settings** tab in the Linking section.

Drag the **Firebase.h** into your project. 

Open app in **TARGETS**, click on **Info** tab and expand **URL Types** section.

Click on **+** button and add a new Url Scheme. Find the key *REVERSED_CLIENT_ID* inside your *GoogleService-Info.plist* file. Copy the value of this key and paste it into the field **URL Schemes**. Let the other fields empty.

Click on **+** button and add a second scheme. Copy the ID of your package *com.example.app* and paste it into **URL Schemes** field. You can find the ID of your package in the **General** tab below **Identity > Bundle Identifier**.

### Step 6: Register the Google Addon

In your AppDelegate, register the Google Addon before you call the **startup** method of Halo.

#### Swift

```swift
import Halo
import HaloSocialGoogle
[...]
let googleAddon = FacebookGoogleAddon()
Halo.Manager.core.registerAddon(addon: googleAddon)
[...]
Halo.Manager.core.startup()
```

#### Objective-C

```objc
#import <Halo/Halo-Swift.h>
#import <HaloObjC/HaloObjC-Swift.h>
#import <HaloSocialGoogle/HaloSocialGoogle.h>
[...]
GoogleSocialAddon *googleAddon = [GoogleSocialAddon new];
[HaloManager.core registerAddon:googleAddon];
[...]
[HaloManager.core startup:^(BOOL success) {
        NSLog(@"Halo started");
}];
```

### Step 7: Login with Google

Use the **loginWithGoogle** method to login with Google. 

#### Swift

```swift
import Halo
import HaloSocialGoogle
[...]
// Your ViewController should implement GIDSignInUIDelegate protocol.
Halo.Manager.auth.loginWithGoogle(uiDelegate: self, stayLoggedIn: false) { (user, error) in
	if error != nil {
		// Something went wrong.
	} else {
		// User logged in succesfully. Do something with "user".
	}
}
```

#### Objective-C

```objc
#import <Halo/Halo-Swift.h>
#import <HaloObjC/HaloObjC-Swift.h>
#import <HaloSocialGoogle/HaloSocialGoogle.h>
[...]
// Your ViewController should implement GIDSignInUIDelegate protocol.
[HaloManager.auth loginWithGoogleWithUIDelegate:self stayLoggedIn:false completionHandler:^(HaloUser * _Nullable user, NSError * _Nullable error) {
        if (error != NULL) {
            // Something went wrong.
        } else {
            // User logged in succesfully. Do something with "user".
        }
}];
```

{% include note.html content="For further information about Firebase SDK visit the [official Firebase documentation page](https://firebase.google.com/docs/ios/setup)" %}