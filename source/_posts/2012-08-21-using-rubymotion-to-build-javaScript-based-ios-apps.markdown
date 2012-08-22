---
layout: post
title: "Using RubyMotion to Build JavaScript Based iOS Apps"
date: 2012-08-21 13:09
comments: true
categories: Mobile Programming
alias: /blog/2012/08/21/building-cordova-apps-and-plugins-with-rubymotion/
published: true
---

{%img right http://f.cl.ly/items/1N1M0h1B3h1P0b1X0B2r/Sencha%20logo.png 85 %}
{%img right http://f.cl.ly/items/3P3L2c0B202C3O3W250Q/cordova_bot.png 85 %}
{%img right http://drop.superformula.com/image/3F2617151N32/rubymotion_icon.png 85 %}
That's right, JavaScript and RubyMotion can play nice together.  And since you're here, you must: love hacking, love Ruby, love iOS, love JavaScript, and love RubyMotion (and/or Laurent for making Ruby to iOS a reality), as none of this would have been possible without any of them (if you could personify the first three, that is)!  What you're about to read is my mash-up of an adventure for teetering on the cutting edge of technology, staying creative, having fun, and the love for code.  So all you JavaScript hackers, grab a rubyist and rejoice 'No more Xcode' (if you're into that sort of thing ;)!
 <!--more-->
I'll be using both [Cordova (PhoneGap)](http://incubator.apache.org/cordova/) and [Sencha Touch 2](http://www.sencha.com/products/touch/) in my examples.  You don't necessarily need to use the aforementioned frameworks, as you could just use JavaScript to write your app and create your own [Cocoa bridge](http://stackoverflow.com/a/9473941/484792) to interact with an app built with RubyMotion, but this is beyond the scope of this article.

{%pullquote%}
{"It's worth mentioning this article will be especially of interest to one that needs to write a custom iOS Plugin for Cordova or add a native UI/feature not available in Sencha Touch. "} Sencha Touch is using NimbleKit (at least /usr/bin/strings thinks so) for it's native SDK bridge and components, there is no (published) interface for one to write a plugin/extension for it.  Which is why I'm using Cordova; allowing for plugin extensions.  Cordova and PhoneGap are effectively the same thing at the moment.  PhoneGap is run by Adobe and will soon be offering other commercial offerings to enhance PhoneGap, while Cordova is managed by the Apache Foundation and should remain the core the PhoneGap offering.
{%endpullquote%}

## Creating a new iOS App

There are several options at your disposal: 

1. The Apple [recommended way](https://developer.apple.com/devcenter/ios/checklist/) with Xcode and [Objective-C](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html) (which is [getting](http://stackoverflow.com/a/9349981/484792) [better](http://clang.llvm.org/docs/ObjectiveCLiterals.html)); 
2. write a web app [using](http://www.sencha.com/products/touch/) [JavaScript](http://jquerymobile.com) and run it directly in Mobile Safari; 
3. wrap a web app in a [native](http://docs.sencha.com/touch/2-0/#!/guide/native_packaging) [shell](http://incubator.apache.org/cordova/), or
4. or use a toolchain that will cross compile to Obj-C byte code like [RubyMotion](http://www.rubymotion.com) or [MonoTouch](http://xamarin.com/monotouch/).

As much as I appreciate Objective-C, Apple's [extensions](http://clang.llvm.org/docs/LanguageExtensions.html#blocks) [to](http://clang.llvm.org/docs/LanguageExtensions.html#objc_arc) [it](http://clang.llvm.org/docs/LanguageExtensions.html#objc_object_literals_subscripting), and what it's done for the mobile space, I simply have more fun writing in JavaScript and Ruby (I'd prefer to just write in JavaSript, but I'll save that for another post).  In this article, I'll describe how I'm using a hybrid of both #3 and #4 to build my product.

[Laurent Sansonetti](http://www.hipbyte.com) has created an amazing toolchain to build iOS apps and since my next project will be running on multiple devices, sadly I won't be using it exclusively.  In my opinion, RubyMotion has the best build tool available for iOS when compared to Xcode and Sencha Touch (the two I've previously used) and as a bonus, the [RubyMotion build process (lib directory)](https://github.com/HipByte/RubyMotion) has been [open-sourced](http://blog.rubymotion.com/post/24197887535/community-open-source-updates)!  

## Slow Pace of JavaScript Apps on Mobile

Many apps exclusively built using JavaScript will need to leverage native device capabilities such as the Network Status, GPS, Camera, Orientation, Accelerometer, Calendar, and Contacts.  There is a [working group](http://www.w3.org/2009/dap/) defining these [device APIs](http://www.w3.org/2009/dap/), but even then the [mobile] browsers may not implement them, so one must resort to a toolchain or wrapper that can utilize the native SDKs for each platform.  Unfortunately, we're several years away from such manufacturer adoption, assuming the working groups and associated parties can get along ;).

In order to work around the slow pace (and capitalist economics) of major device manufacturers and software producers, I'll be using a combination of my favorite mobile JavaScript frameworks and iOS publishing toolset in this article: [Cordova](http://incubator.apache.org/cordova/), [Sencha Touch](http://www.sencha.com/products/touch/), and [RubyMotion](http://www.rubymotion.com).

## Building JavaScript (Cordova/Sencha Touch) Apps with RubyMotion

{%pullquote%}
It could be argued that you could use just one framework, either Cordvoa or Sencha Touch to build the iOS app, as they both have build tools (or a build process) and native Cocoa SDKs.  {" Both Cordova and Sencha Touch support multiple platforms (iOS, Android, Windows Mobile, etc.), but we're going to focus on iOS in this article."}
{%endpullquote%}

It's assumed that you've already setup a RubyMotion app, understand or have used Cordova, and are familiar with Sencha Touch.  I won't be speaking to how each of these products can work together or their corresponding setups.

### Project Setup

RubyMotion can be setup to [link to third-party libraries](http://www.rubymotion.com/developer-center/guides/project-management/#_using_3rd_party_libraries), which is needed to build a Cordova app with RubyMotion.  Below is a example Rakefile for your RubyMotion project.

{% gist 3425963 %}

### Directory Structure

<table>
    <thead>
    <tr>
        <th style="width: 155px">
            File/Directory
        </th>
        <th>
            Purpose
        </th>
    </tr>
    </thead>
    <tr>
        <td>
            Rakefile
        </td>
        <td>
            This file contains the configuration of the project, as well as a default set of tasks. It can be edited to change the RubyMotion configuration or add new build tasks.
        </td>
    </tr>
     <tr>
        <td>
            app/
        </td>
        <td>
            This directory contains the code of the project.
        </td>
    </tr>
    <tr>
        <td>
            resources/
        </td>
        <td>
            This directory contains the resources that will automatically be included in the RubyMotion iOS project.
        </td>
    </tr>
    <tr>
        <td>
            resources/www/
        </td>
        <td>
            This directory will be used by Sencha Touch to host the app code and styles.
        </td>
    </tr>
    <tr>
        <td>
            vendor/
        </td>
        <td>
            This directory contains the vendor libraries used in the project.  These can be both Ruby and JavaScript.
        </td>
    </tr>
</table>

This app structure was borrowed from RubyMotion but modified to my liking to allow me to build for multiple platforms and technologies for my products.  I've separated the ```app/``` directory by scope, ie: ios, android, and webview, and rely on the Rakefile to do all the building for CoffeeScript, Sass, Ruby, and other platforms and plugin builds.  The ```vendor/``` directory has *CordovaLib* copied from ```~/Documents/CordovaLib``` and the Sencha Touch SDK.

### App Setup

All Cordova wants is some JavaScript buddies to talk to.  It's just waiting for API calls!  Sencha Touch already has adapters for it's Device API calls, and will detect through ```Ext.browser.is```whether you're in the simulator, Cordova (PhoneGap), or Sencha.  You could override some of these adapters (or create your own) and hook them into your custom Cordova plugins.  You'll be writing your own interfaces for the Cordova plugins which will effectively be calling the RubyMotion ruby classes.

Here are two boilerplate classes that are needed for a base Cordova project.  Take note of *main_view_controller.rb* as it's the one inheriting *CDVViewController* which acts as the bridge to receive all the JavaScript callbacks, setup plugins, whitelists, etc. from the webview (Sencha in my case).  Take a look at *CDVViewController.m* to learn more and hack away in *main_view_controller.rb* at your desire!

{%gist 3429508 %}

You can alter the ```@viewController.wwwFolderName``` and ```@viewController.startPage``` properties to point to your app startpoint.  The defaults are *www* and *index.html* respectively, which are relative to the ```resources/``` directory.

Assuming you have your app setup, you can run ```rake``` which should build with RubyMotion and launch your app.

I'll write a few more articles in the adventures JavaScript and mobile as I move along in my projects.  Let me know what you think!
