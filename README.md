# Test Dynamic Feature

This project is intended just to show a possible bug when building an application with dynamic features.

## Scenario
Having a project with:
- An application module
- A dynamic feature module
- A library module

The dynamic feature has a dependency with the library module, and the library module has a dependency that defines a provider in its manifest (an image picker for instance).

If wy try to run the application through bundle generation:

`./gradlew extractApksForDebug`

We get the error:

```
Execution failed for task ':app:makeApkFromBundleForDebug'.
> A failure occurred while executing com.android.build.gradle.internal.tasks.BundleToApkTask$BundleToolRunnable
   > Multiple meta-data entries with the same name are found inside provider:com.github.dhaval2404.imagepicker.ImagePickerFileProvider: name: "meta-data"
     attribute {
       namespace_uri: "http://schemas.android.com/apk/res/android"
       name: "name"
       value: "android.support.FILE_PROVIDER_PATHS"
       resource_id: 16842755
     }
     attribute {
       namespace_uri: "http://schemas.android.com/apk/res/android"
       name: "resource"
       value: "@xml/image_picker_provider_paths"
       resource_id: 16842789
       compiled_item {
         ref {
           id: 2148335616
           name: "xml/image_picker_provider_paths"
           type_flags: 1
         }
       }
     }
     , name: "meta-data"
     attribute {
       namespace_uri: "http://schemas.android.com/apk/res/android"
       name: "name"
       value: "android.support.FILE_PROVIDER_PATHS"
       resource_id: 16842755
     }
     attribute {
       namespace_uri: "http://schemas.android.com/apk/res/android"
       name: "resource"
       value: "@xml/image_picker_provider_paths"
       resource_id: 16842789
       compiled_item {
         ref {
           id: 2131886080
           name: "xml/image_picker_provider_paths"
           type_flags: 1
         }
       }
     }
```

As a workaround, you can set `dist:removable` to `true` on the dynamic features Manifest to avoid fusing the dynamic feature APK into the base module:

```
<dist:delivery>
  <dist:install-time>
    <dist:removable dist:value="true" />
  </dist:install-time>
</dist:delivery>
```

Maybe this error is related to duplicate resources:
- https://medium.com/androiddevelopers/a-patchwork-plaid-monolith-to-modularized-app-60235d9f212e (styling issues) [+Info](https://github.com/nickbutcher/plaid/blob/main/core/src/main/res/values/styles.xml#L74)
- https://github.com/google/bundletool/issues/142