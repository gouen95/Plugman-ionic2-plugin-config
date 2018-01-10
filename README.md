# Plugman-ionic2-plugin-config
## Configuration for custom node plugin for Ionic 2 with Plugman. <br />
## We will only be using Plugman to generate the template for the custom plugin. <br />

**0. Requirements:**
  1. [Node.js](https://nodejs.org/en/download/)
  2. Ionic & Cordova module
  3. [Plugman](https://www.npmjs.com/package/plugman)
  <br />

**1. Install Ionic & Cordova using npm**
```
sudo npm install -g cordova ionic
```
<br />

**1.1. Create Ionic project, add ios/android platform and build platforms**
```
ionic start
cd <project name>
ionic cordova platform add ios
ionic cordova platform add android
ionic cordova build ios
ionic cordova build android
```


**2. Create Cordova plugin using [Plugman](https://www.npmjs.com/package/plugman)**
```
plugman create --name FirstPlugin --plugin_id cordova-plugin-firstplugin --plugin_version 0.0.1
```
<br />

**2.1. Add platform into the plugin (optional)**
```
cd FirstPlugin
plugman platform add --platform_name android
plugman platform add --platform_name ios
```
<br />

**3. Open plugin.xml to add extra configurations for each platform**
```
<platform name="ios">
  <config-file parent="/*" target="config.xml">
     <feature name="FirstPlugin">
        <param name="ios-package" value="FirstPlugin" />
     </feature>
  </config-file>
  <source-file src="src/ios/FirstPlugin.m" />

  <!-- Extra added config plist for camera usage -->
  <config-file target="*-Info.plist" parent="NSCameraUsageDescription">
     <string>for camera usage</string>
  </config-file>

  <!-- Extra added frameworks -->
  <!-- Requires creator to make a folder and import custom bundled ios framework into it-->
  <framework src="src/ios/Frameworks/XXXFramework.framework" custom="true"/> 
  <framework src="CoreGraphics.framework" weak="true" />
</platform>
```
<br />

**4. (SKIPPABLE)Making sure that FirstPlugin.js and FirstPlugin.m/FirstPlugin.java is in-sync**
```
<!-- FirstPlugin.js -->
var exec = require('cordova/exec');

exports.coolMethod = function (arg0, success, error) {
    exec(success, error, 'FirstPlugin', 'coolMethod', [arg0]);
};

exports.coolMethod2 = function (arg0, success, error) {
    exec(success, error, 'FirstPlugin', 'coolMethod2', [arg0]);
};

<!-- FirstPlugin.m -->
/********* FirstPlugin.m Cordova Plugin Implementation *******/

#import <Cordova/CDV.h>
#import "XXXFramework.framework/Headers/XXXFramework.h"

@interface FirstPlugin : CDVPlugin {
  // Member variables go here.
}

- (void)coolMethod:(CDVInvokedUrlCommand*)command;
@end

@implementation FirstPlugin

- (void)coolMethod:(CDVInvokedUrlCommand*)command
{
    CDVPluginResult* pluginResult = nil;
    NSString* echo = [command.arguments objectAtIndex:0];

    if (echo != nil && [echo length] > 0) {
        pluginResult = [CDVPluginResult resultWithStatus:CDVCommandStatus_OK messageAsString:echo];
    } else {
        pluginResult = [CDVPluginResult resultWithStatus:CDVCommandStatus_ERROR];
    }

    [self.commandDelegate sendPluginResult:pluginResult callbackId:command.callbackId];
}

-(void)coolMethod2:(CDVInvokedUrlCommand*)command
{
    CDVPluginResult* pluginResult = nil;
    NSMutableDictionary* echoDict = [command.arguments objectAtIndex:0];
}

@end

```
<br />

**5. (![#f03c15](https://placehold.it/15/f03c15/000000?text=+!) IMPORTANT!! ![#f03c15](https://placehold.it/15/f03c15/000000?text=+!))Generate node package.json(We are not using plugman to install plugin to ionic project)**
```
sudo plugman createpackagejson .

<!-- It will ask for name, put FirstPlugin -->
```
<br />

**6. Navigate to ionic project and install plugin**
```
sudo ionic cordova plugin add <path/to/your/customplugin>
```
<br />

**7. Example code to run the plugin in app.component.ts**
```
import { Component } from '@angular/core';
import { Platform } from 'ionic-angular';
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';

import { HomePage } from '../pages/home/home';

declare var cordova: any;

var success = function(result) {
  alert(JSON.stringify(result, undefined, 2));
}
var failure = function(result) {
  alert(JSON.stringify(result, undefined, 2));
}

var cars = ["Saab", "Volvo", "BMW"];

@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage:any = HomePage;

  constructor(platform: Platform, statusBar: StatusBar, splashScreen: SplashScreen) {
    platform.ready().then(() => {
      // Okay, so the platform is ready and our plugins are available.
      // Here you can do any higher level native things you might need.

      cordova.plugins.FirstPlugin.coolMethod({
        cars
      },success,failure);

      statusBar.styleDefault();
      splashScreen.hide();
    });
  }
}

```

# You are now done!
