# soloader-issues
A sample demonstrate the soloader library issues when used in Android App Bundle

* Device: pixel2
* Android Version: 11

## Issues steps

* buildAppBundle(release or debug)

  ```
  ./gradlew :app:bundleRelease
  ```

* build apks use bundle-tool

  ```
  java -jar $BUNDLE_TOOL.jar build-apks --bundle=app-release.aab --output=app.apks --overwrite
  ```

* get device spec  use bundle-tool

  ```
  java -jar $BUNDLE_TOOL.jar get-device-spec --output=pixel2.json
  ```

  

* extract apks use bundle-tool

  ```
  java -jar $BUNDLE_TOOL.jar extract-apks --apks=app.apks --device-spec=pixel2.json --output-dir=pixel2 --modules=_ALL_
  ```

* install base apk

  ```
  adb install pixel2/base-master_2.apk
  ```

* run app and then press the bottom right fab button try load **yoga** library. Ops, crash...

  <img src="/Users/didi/Code/Github/YogaIssues/screenshot/1631850290379.jpg" alt="1631850290379" style="zoom:50%;" /> 

## Probably Reason

When use Android App Bundle, the native libraries will no compress, when run the app, can load the library from the zip directoly.

So, the SoLoader can not find the native library in system lib directory, then copy the libraries to lib-main directory, when load library, it load with AppSoSource

```
// DirectorySoSource:70
if ((flags & RESOLVE_DEPENDENCIES) != 0) {
      loadDependencies(soFile, loadFlags, threadPolicy);
    }
```

this will called load the dependencies, but when load libfb.so, it crash.

I don't know this is libfb.so's issues or Soloader issues, who can tell me.

And i don't text this issues in other simple native libraries.

