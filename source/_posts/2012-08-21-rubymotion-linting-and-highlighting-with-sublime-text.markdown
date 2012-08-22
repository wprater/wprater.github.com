---
layout: post
title: "RubyMotion Linting and Highlighting with Sublime Text"
date: 2012-07-24 12:44
comments: true
categories: Mobile Programming
---

Looking to build a new iOS project with [RubyMotion](http://www.rubymotion.com) and you're a fan of [Sublime Text](http://www.sublimetext.com) too?  Here are a few tips to get syntax highlighting, linting, and snippets running smoothly on your machine. <!--more-->  There is a great RubyMotion [bundle](https://github.com/haraken3/SublimeRubyMotionBuilder) by [Kentaro Hara](https://twitter.com/haraken3) already available, which you can use to get the Cocoa SDK syntax highlighting, code completion, and some lovely snippets!  The syntax highlighting in this bundle is different than the normal Ruby bundle, which I find odd.  

{% pullquote %}
I'm a fan of real time linting and since the usual linting provided by ```ruby -wc``` won't work here because of the extensions to the Ruby language (Ruby 2.0 should have named parameter support) [Laurent Sansonetti](http://www.hipbyte.com) has made. {" The syntax used to define Objective-C selectors was added to RubyMotion and is not part of the Ruby standard.
 "} You'll need to link the linter output to the custom ruby executable that's available as part of the RubyMotion build. 
{% endpullquote %}

Luckily, if you use the [SublimeLinter](https://github.com/SublimeLinter/SublimeLinter) bundle for your linting joy in Sublime Text, you're in luck.  The authors have made it simple to write new linter extensions and this is a perfect use-case.

### Create the RubyMotion linter language for Sublime Text and Abacus

You need to create a new linting module that can be used in the *executable_map* and *syntax_map* options in SublimeLinter.  This module will effectively be a copy of the ruby module, with default config options set.

{% img right http://f.cl.ly/items/2R1z0s0u1d3L053O1x16/Image%202012.08.21%2011:05:40%20AM.png %}
Create a new file named ```ruby_motion.py``` in your SublimeLinter modules directory.  The filename, excluding the extension, is the module name which is used in the *executable_map* and *syntax_map* options one can override in their preferences.  You can find the modules directory in the Sublime Text packages directory on your machine.  Find this directory by using the Browse Packages... menu option and browse to ```SublimeLinter/sublimelinter/modules``` directory.

Below is the file to create that will create some defaults for the ```ruby_motion``` linting module, which you can change per project or through user settings if need be. 
 
{% gist 3413799 ruby_motion.py %}

The ```CONFIG``` dictionary sets up the defaults for your new ```ruby_motion``` module so that the language is *RubyMotion* and the executable path is in */Library/RubyMotion/bin/ruby* which should be perfect for a default install of RubyMotion and the RubyMotionBuilder bundle.  If you need to change these, you can do so in your user preferences for the SublimeLinter bundle and edit the *executable_map* and *syntax_map* options.

You should now have a highlighting, linting, and pure joy while writing RubyMotion in Sublime Text.
