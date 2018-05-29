[![Join the chat at https://gitter.im/mongodb/stitch](https://badges.gitter.im/mongodb/stitch.svg)](https://gitter.im/mongodb/stitch?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

# MongoDB Stitch Android/Java SDK 

The official [MongoDB Stitch](https://stitch.mongodb.com/) SDK for Android/Java.

### Index
- [Documentation](#documentation)
- [Discussion](#discussion)
- [Installation](#installation)
- [Example Usage](#example-usage)

## Documentation
* [API/Javadoc Documentation](https://s3.amazonaws.com/stitch-sdks/android/docs/4.0.0/index.html)
* [MongoDB Stitch Documentation](https://docs.mongodb.com/stitch/)

## Discussion
* [MongoDB Stitch Users - Google Group](https://groups.google.com/d/forum/mongodb-stitch-users)
* [MongoDB Stitch Announcements - Google Group](https://groups.google.com/d/forum/mongodb-stitch-announce)

## Installation

### Android

Add the following to the build.gradle for your app module:

```gradle
implementation 'org.mongodb:stitch-android-sdk:4.0.0'
```

For customized dependencies use the following:

```gradle
implementation 'org.mongodb:stitch-android-core:4.0.0'
implementation 'org.mongodb:stitch-android-services-aws-s3:4.0.0'
implementation 'org.mongodb:stitch-android-services-aws-ses:4.0.0'
implementation 'org.mongodb:stitch-android-services-http:4.0.0'
implementation 'org.mongodb:stitch-android-services-mongodb-remote:4.0.0'
implementation 'org.mongodb:stitch-android-services-twilio:4.0.0'
```

### Server/Java

Add the following to the build.gradle for your module:

```gradle
implementation 'org.mongodb:stitch-server-sdk:4.0.0'
```

For customized dependencies use the following:

```gradle
implementation 'org.mongodb:stitch-server-core:4.0.0'
implementation 'org.mongodb:stitch-server-services-aws-s3:4.0.0'
implementation 'org.mongodb:stitch-server-services-aws-ses:4.0.0'
implementation 'org.mongodb:stitch-server-services-http:4.0.0'
implementation 'org.mongodb:stitch-server-services-mongodb-local:4.0.0'
implementation 'org.mongodb:stitch-server-services-mongodb-remote:4.0.0'
implementation 'org.mongodb:stitch-server-services-twilio:4.0.0'
```

## Example Usage

### Creating a new app with the SDK (Android)

#### Set up an application on Stitch
1. Go to [https://stitch.mongodb.com/](https://stitch.mongodb.com/) and log in to MongoDB Atlas.
2. Create a new app in your project with your desired name.
3. Go to your app in Stitch via Atlas by clicking Stitch Apps in the left side pane and clicking your app.
3. Copy your app's client app id by going to Clients on the left side pane and clicking copy on the App ID section.
4. Go to Providers from Users in the left side pane and edit and enable "Allow users to log in anonymously".

#### Set up a project in Android Studio using Stitch
1. Download and install [Android Studio](https://developer.android.com/studio/index.html).
2. Start a new Android Studio project.
	* Note: The minimum supported API level is 21 (Android 5.0 Lollipop)
	* Starting with an empty activity is ideal
3. In your build.gradle for your app module, add the following to your dependencies block:

	```gradle
    implementation 'org.mongodb:stitch-android-sdk:4.0.0'
    ```
4. Android Studio will prompt you to sync your changes in your project; hit Sync Now.

#### Set up an Android Virtual Device

1. In Android Studio, go to Tools, Android, AVD manager.
2. Click Create Virtual Device.
3. Select a device that should run your app (the default is fine).
4. Select and download a recommended system image of your choice (the latest is fine).
	* x86_64 images are available in the x86 tab.
5. Name your device and hit finish.

#### Using the SDK

##### Set up app info
1. Create a resource values file in your app (e.g. res/values/mongodb-stitch.xml) and set the following using the app id you copied earlier in place of YOUR_APP_ID:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="stitch_client_app_id">YOUR_APP_ID</string>
</resources>
```

##### Logging In
1. Since we enabled anonymous log in, let's log in with it; add the following anywhere in your code:

	```java
	final StitchAppClient client = Stitch.getDefaultAppClient();
    client.getAuth().loginWithCredential(new AnonymousCredential()).addOnCompleteListener(
        new OnCompleteListener<StitchUser>() {
      @Override
      public void onComplete(@NonNull final Task<StitchUser> task) {
        if (task.isSuccessful()) {
          Log.d("myApp", String.format(
              "logged in as user %s with provider %s",
              task.getResult().getId(),
              task.getResult().getLoggedInProviderType()));
        } else {
          Log.e("myApp", "failed to log in", task.getException());
        }
      }
    });
	```

2. Now run your app in Android Studio by going to run, Run 'app'. Use the Android Virtual Device you created previously
3. Once the app is running, open up Logcat in the bottom of Android studio and you should see the following log message:

	```
	logged in as user 5b0483778f25b978044aca76 with provider anon-user
	```
	
##### Getting a StitchAppClient without Stitch.getDefaultAppClient

In the case that you don't want a default initialized StitchAppClient by setting up the resource values, you can use the following code once to initialize a client for a given app id that you copied earlier:

```java
final StitchAppClient client = Stitch.initializeAppClient(StitchAppClientConfiguration.Builder.forApp("YOUR_APP_ID"));
```

You can use the client returned there or anywhere else in your app you can use the following:

```java
final StitchAppClient client = Stitch.getAppClient("YOUR_APP_ID");
```