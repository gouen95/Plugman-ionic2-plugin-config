# Plugman-ionic2-plugin-config
#### Configuration for custom node plugin for Ionic 2 with Plugman. <br />
#### We will only be using Plugman to generate the template for the custom plugin. <br />

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

**4. (SKIPPABLE) Making sure that FirstPlugin.js and FirstPlugin.m/FirstPlugin.java is in-sync**
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

**5. (![#f03c15](https://placehold.it/15/f03c15/000000?text=+!) IMPORTANT!! ![#f03c15](https://placehold.it/15/f03c15/000000?text=+!)) Generate node package.json(We are not using plugman to install plugin to ionic project)**
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
# Below are extras this is to eliminate manual work for importing and configuring xcode's config.
## Extra : [cordova-custom-config for ios](https://github.com/dpa99c/cordova-custom-config)
**1. Adding cordova custom config**
```
cordova plugin add cordova-custom-config
```
<br />

**2. Add lines in project's config.xml**
```
<platform name="ios">
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-QUOTE_DEFAULT" value="YES" />
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-QUOTE_BOTH" quote="both" value="YES" />
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-QUOTE_KEY" quote="key" value="YES" />
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-QUOTE_VALUE" quote="value" value="YES" />
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-QUOTE_NONE" quote="none" value="YES" />
  <custom-preference name="ios-XCBuildConfiguration-BUiLD_TYPE_DEFAULT" value="YES" />
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-BUiLD_TYPE_DEBUG" value="YES" />
  <custom-preference buildType="release" name="ios-XCBuildConfiguration-BUiLD_TYPE_RELEASE" quote="both" value="YES" />
  <custom-preference name="ios-XCBuildConfiguration-ENABLE_BITCODE" value="NO" />
  <custom-preference name="ios-XCBuildConfiguration-ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES" value="YES" />
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-IPHONEOS_DEPLOYMENT_TARGET" value="9.1" />
  <custom-preference buildType="release" name="ios-XCBuildConfiguration-IPHONEOS_DEPLOYMENT_TARGET" value="7.0" />
  <custom-preference buildType="debug" name="ios-XCBuildConfiguration-QUOTE_BOTH" quote="both" value="YES" xcconfigEnforce="true" />
  <custom-preference name="ios-XCBuildConfiguration-TARGETED_DEVICE_FAMILY" value="1" /
</platform>
```
<br />

## Extra 2 : [Add framework to linked and embedded](https://stackoverflow.com/questions/36650522/custom-cordova-plugin-add-framework-to-embedded-binaries/36723619)
**1. Change framework src embed=true to weak=true**
```
 <platform name="ios">
    <config-file parent="/*" target="config.xml">
       <feature name="GamificationPlugin">
          <param name="ios-package" value="GamificationPlugin" />
       </feature>
    </config-file>
    <source-file src="src/ios/GamificationPlugin.m" />

    <framework src="src/ios/Frameworks/HandyJSON.framework" custom="true" embed="true" />
    <framework src="src/ios/Frameworks/GamificationFramework.framework" custom="true" embed="true" />
    <framework src="QuartzCore.framework"  weak="true" />
    <framework src="CoreGraphics.framework" weak="true" />
    <framework src="UIKit.framework" weak="true" />

    <hook type="after_platform_add" src="hooks/add_embedded.js" />
 </platform>
```
<br />

**2. Add hook in plugin.xml and create new folder called hook for add_embedded.js**
```
<hook type="after_platform_add" src="hooks/add_embedded.js" />
```
<br />

**3. Install [node-xcode](https://github.com/apache/cordova-node-xcode), download from github and navigate to plugin's folder  and type below code**
```
npm install <path/of/downloaded node-xcode/>
```
<br />

**3.1 (OPTIONAL) Install in the user's root as well?**
```
cd ~
npm install <path/of/downloaded node-xcode/>
```
<br />

**4. Copy below code to add_embedded.js***
```
'use strict';

const xcode = require('xcode'),
    fs = require('fs'),
    path = require('path');

module.exports = function(context) {
    if(process.length >=5 && process.argv[1].indexOf('cordova') == -1) {
        if(process.argv[4] != 'ios') {
            return; // plugin only meant to work for ios platform.
        }
    }

    function fromDir(startPath,filter, rec, multiple){
        if (!fs.existsSync(startPath)){
            console.log("no dir ", startPath);
            return;
        }

        const files=fs.readdirSync(startPath);
        var resultFiles = []
        for(var i=0;i<files.length;i++){
            var filename=path.join(startPath,files[i]);
            var stat = fs.lstatSync(filename);
            if (stat.isDirectory() && rec){
                fromDir(filename,filter); //recurse
            }

            if (filename.indexOf(filter)>=0) {
                if (multiple) {
                    resultFiles.push(filename);
                } else {
                    return filename;
                }
            }
        }
        if(multiple) {
            return resultFiles;
        }
    }

    function getFileIdAndRemoveFromFrameworks(myProj, fileBasename) {
        var fileId = '';
        const pbxFrameworksBuildPhaseObjFiles = myProj.pbxFrameworksBuildPhaseObj(myProj.getFirstTarget().uuid).files;
        for(var i=0; i<pbxFrameworksBuildPhaseObjFiles.length;i++) {
            var frameworkBuildPhaseFile = pbxFrameworksBuildPhaseObjFiles[i];
            if(frameworkBuildPhaseFile.comment && frameworkBuildPhaseFile.comment.indexOf(fileBasename) != -1) {
                fileId = frameworkBuildPhaseFile.value;
                pbxFrameworksBuildPhaseObjFiles.splice(i,1); // MUST remove from frameworks build phase or else CodeSignOnCopy won't do anything.
                break;
            }
        }
        return fileId;
    }

    function getFileRefFromName(myProj, fName) {
        const fileReferences = myProj.hash.project.objects['PBXFileReference'];
        var fileRef = '';
        for(var ref in fileReferences) {
            if(ref.indexOf('_comment') == -1) {
                var tmpFileRef = fileReferences[ref];
                if(tmpFileRef.name && tmpFileRef.name.indexOf(fName) != -1) {
                    fileRef = ref;
                    break;
                }
            }
        }
        return fileRef;
    }

    const xcodeProjPath = fromDir('platforms/ios','.xcodeproj', false);
    const projectPath = xcodeProjPath + '/project.pbxproj';
    const myProj = xcode.project(projectPath);

    function addRunpathSearchBuildProperty(proj, build) {
       const LD_RUNPATH_SEARCH_PATHS =  proj.getBuildProperty("LD_RUNPATH_SEARCH_PATHS", build);
       if(!LD_RUNPATH_SEARCH_PATHS) {
          proj.addBuildProperty("LD_RUNPATH_SEARCH_PATHS", "\"$(inherited) @executable_path/Frameworks\"", build);
       } else if(LD_RUNPATH_SEARCH_PATHS.indexOf("@executable_path/Frameworks") == -1) {
          var newValue = LD_RUNPATH_SEARCH_PATHS.substr(0,LD_RUNPATH_SEARCH_PATHS.length-1);
          newValue += ' @executable_path/Frameworks\"';
          proj.updateBuildProperty("LD_RUNPATH_SEARCH_PATHS", newValue, build);
       }
    }

    myProj.parseSync();
    addRunpathSearchBuildProperty(myProj, "Debug");
    addRunpathSearchBuildProperty(myProj, "Release");

    // unquote (remove trailing ")
    var projectName = myProj.getFirstTarget().firstTarget.name.substr(1);
    projectName = projectName.substr(0, projectName.length-1); //Removing the char " at beginning and the end.

    const groupName = 'Embed Frameworks ' + context.opts.plugin.id;
    const pluginPathInPlatformIosDir = projectName + '/Plugins/' + context.opts.plugin.id;

    process.chdir('./platforms/ios');
    const frameworkFilesToEmbed = fromDir(pluginPathInPlatformIosDir ,'.framework', false, true);
    process.chdir('../../');

    if(!frameworkFilesToEmbed.length) return;

    myProj.addBuildPhase(frameworkFilesToEmbed, 'PBXCopyFilesBuildPhase', groupName, myProj.getFirstTarget().uuid, 'frameworks');

    for(var frmFileFullPath of frameworkFilesToEmbed) {
        var justFrameworkFile = path.basename(frmFileFullPath);
        var fileRef = getFileRefFromName(myProj, justFrameworkFile);
        var fileId = getFileIdAndRemoveFromFrameworks(myProj, justFrameworkFile);

        // Adding PBXBuildFile for embedded frameworks
        var file = {
            uuid: fileId,
            basename: justFrameworkFile,
            settings: {
                ATTRIBUTES: ["CodeSignOnCopy", "RemoveHeadersOnCopy"]
            },

            fileRef:fileRef,
            group:groupName
        };
        myProj.addToPbxBuildFileSection(file);


        // Adding to Frameworks as well (separate PBXBuildFile)
        var newFrameworkFileEntry = {
            uuid: myProj.generateUuid(),
            basename: justFrameworkFile,

            fileRef:fileRef,
            group: "Frameworks"
        };
        myProj.addToPbxBuildFileSection(newFrameworkFileEntry);
        myProj.addToPbxFrameworksBuildPhase(newFrameworkFileEntry);
    }

    fs.writeFileSync(projectPath, myProj.writeSync());
    console.log('Embedded Frameworks In ' + context.opts.plugin.id);
};
```
