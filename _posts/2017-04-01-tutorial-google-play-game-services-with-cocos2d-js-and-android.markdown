---
comments: true
date: 2017-04-01 16:25:05+00:00
layout: post
slug: tutorial-google-play-game-services-with-cocos2d-js-and-android
title: '[Tutorial] Google Play Game Services with Cocos2d-js and Android'
categories:
- Android
- Game
tags:
- Android
- cocos
- cocos2d
- cocos2d-js
- cocos2d-x
- games
- google play
- google play game services
- helper
- howto
- js
- Tutorial
---

### Welcome!


In this post, I'm going to show you how to use Google Play Game Services with your Cocos2d-js or Cocos2d-html5 game!
I'm not going go show you how to use it with iOS, this tutorial is only about Android.

Let's take a look!

<figure class='center '>
  <a href="http://www.cocos2d-x.org/attachments/802/cocos2dx_landscape.png"><img src="http://www.cocos2d-x.org/attachments/802/cocos2dx_landscape.png" alt=""></a>
  <figcaption></figcaption>
</figure>

<!-- more -->

{% include banner-top.html %}

## I - About Cocos-js

[Cocos2d-JS](https://github.com/cocos2d/cocos2d-js) is an open source game engine for web games and native games. It has a high performance, is user-friendly and supports multi-platform development. Supported platforms include web, Android, iOS, Windows Phone 8, Mac, Windows, etc. Cocos2d-JS makes 2D game programming easier and faster. It clarifies the key components of 2D game programming by being easy to learn and having an easy to use API. All of this combined makes it an outstanding framework compared to others. Cocos2d-JS is the HTML5 version of Cocos2d-x, includes [Cocos2d-html5](https://github.com/cocos2d/cocos2d-html5) and Cocos2d-x JavaScript Binding (JSB). Using Cocos2d-JS, you can write games in JavaScript and run your games on browsers that support HTML5. The API is completely compatible between Cocos2d-html5 and Cocos2d-x JSB. This means that Cocos2d-JS games can be run using Cocos2d's "JavaScript Binding" on Cocos2d-x without or with very little modification.

It's my favorite game engine! And now, Cocos support 3D game development! Ok, let's return to the tutorial...

You can read[ this forum post](http://discuss.cocos2d-x.org/t/google-play-game-services/7190), it's my source to this post, but I updated some steps :)


## II - Creating a Google Play Game Services project


Open the [https://play.google.com/apps/publish/](https://play.google.com/apps/publish/) go to Game Services page and add a new app.

<figure class='center '>
  <a href="{{site.url}}/images/services.png"><img src="{{site.url}}/images/services.png" alt=""></a>
</figure>

Now, select **I don’t use any Google APIs in my game yet** and add the necessary info for your project.

<figure class='center '>
  <a href="{{site.url}}/images/service_reg.png"><img src="{{site.url}}/images/service_reg.png" alt=""></a>
</figure>

Now, download the **BaseGameUtils** [provided by Google in this link](https://github.com/playgameservices/android-basic-samples).


## III - Preparing your project

You'll need to add the **android-support-v4.jar** and **google-play-services.jar** to you project folder.

You can get these libs at:

  * $ANDROID_SDK_PATH/extras/android/support/v4
  * $ANDROID_SDK_PATH/extras/google/google_play_services/libproject/google-play-services_lib/libs

Copy these .jars to **your_project_folder/frameworks/runtime-src/proj.android/libs**.

Get the project id bellow your project name:

<figure class='center '>
  <a href="{{site.url}}/images/turnoff.png"><img src="{{site.url}}/images/turnoff.png" alt=""></a>
</figure>

Now, in your AndroidManifest.xml, add the following meta tag:

{% highlight xml %}
<meta-data android:name="com.google.android.gms.games.APP_ID" android:value="YOUR_PROJECT_ID" />
{% endhighlight %}

Just build and ruin you app now to check if there is no incompatibility with these new libs and some other lib that you should be using.

If it's building, you can go to the next step :)


## IV - Linking BaseGameUtils with Cocos2d-js

Go to **/frameworks/runtime-src/proj.android/src/org/cocos2dx/javascript** folder and add these files:

  1. BaseGameActivity.java
  2. GameHelper.java
  3. BaseGameUtils.java
  4. GameHelperUtils.java

You can get all files on **android-basic-samples-master/BasicSamples/libraries/BaseGameUtils/src/main/java/com/google/example/games/basegameutils**

Update the package of all .java to:

{% highlight java %}
package org.cocos2dx.javascript;
{% endhighlight %}

Add the following import to your **GameHelperUtils.java** and **BaseGameUtils.java**:

{% highlight java %}
import com.aronbordin.turnoffthelights.R;
{% endhighlight %}

Just make sure to change use your Android package name!

Open you **BaseGameActivity.java** and add an import and update the class definition to:

{% highlight java %}  
import org.cocos2dx.lib.Cocos2dxActivity;
public abstract class BaseGameActivity extends Cocos2dxActivity implements
{% endhighlight %}


Now you can add your own methods and features in the **AppActivity.java**.

[Check this gist to read my AppActivity](https://gist.github.com/aron-bordin/915215be9963f6467696), you can copy it and just change your package name and **LEADERBOARD_ID**.

Edit the file at **/frameworks/runtime-src/proj.android/res/values/strings.xml** and add some new values:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="gamehelper_sign_in_failed">Failed to sign in. Please check your network connection and try again.</string>
    <string name="gamehelper_app_misconfigured">The application is incorrectly configured. Check that the package name and signing certificate match the client ID created in Developer Console. Also, if the application is not yet published, check that the account you are trying to sign in with is listed as a tester account. See logs for more information.</string>
    <string name="gamehelper_license_failed">License check failed.</string>
    <string name="gamehelper_unknown_error">Unknown error.</string>
    <string name="sign_in_failed">Failed to sign in. Please check your network connection and try again.</string>
    <string name="app_misconfigured">The application is incorrectly configured. Check that the package name and signing certificate match the client ID created in Developer Console. Also, if the application is not yet published, check that the account you are trying to sign in with is listed as a tester account. See logs for more information.</string>
    <string name="license_failed">License check failed.</string>
    <string name="unknown_error">Unknown error.</string>
</resources>
{% endhighlight %}

## V - Linking your app

Go to Google publish, Linked apps and add a new Android project. Use the correct package name and SHA1.

Build and run your project, if everything is working, you'll be able to login with Google :)

## VI - Calling Java methods

Now if your Google Game Services is working as expected, you can call Java methods.

[You can read more about it here: http://www.cocos2d-x.org/wiki/Invoking_Android_Java_methods_from_JavaScript](http://www.cocos2d-x.org/wiki/Invoking_Android_Java_methods_from_JavaScript)

You can use it to list friends, show the leaderboard, add achievements, etc.

To show the Leaderboard, for example, use this JS method:

{% highlight js %}
jsb.reflection.callStaticMethod("org/cocos2dx/javascript/AppActivity", "showLeaderboards", "()V");
{% endhighlight %}


That's it :)

If you have any problem, feel free to comment here :)

Thx for reading,

Aron Bordin.

{% include banner-bottom.html %}