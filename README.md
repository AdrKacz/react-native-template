# react-native-template
React Native Template Projet with GitHub Action to build and publish to TestFlight and Google Play Console

# Create React Native App

## Create `.\template` folder where the app is.

```
npx react-native init template
cd template
yarn start
```

## Check it runs on iOS and Android.

```
yarn ios
```

```
yarn android
```

# Fastlane for Android

Follow step at [Fastlane Androidn Setup](https://docs.fastlane.tools/getting-started/android/setup/).

## Initialise Fastline and test it

```
cd android
fastlane init
fastlane test
```

## Get Google credentials

- Open Google Play Console
- Go to Setup > API Access
- Click on **Create new service account** (*bottom right, in gray*)
- Click on Google Cloud Platform link
- Choose a `Service account name` (*react-native-template*)
- Click on **Done**
- Click on **Select a role**, and search for **Service Account User**
- Click on **Done**
- Click on the *three dots* to the right, and select **Manage Keys**
- Click on **ADD KEY > Create New Key**
- Select **JSON**, and click **Create**
- Save key in `./fastlane/pc-api-***.json`
- Add `./fastlane/.gitignore` with `*.json`
- Return to Google Play Console, and click on **Done**
- Click on **Grant Access**
- Select the *permissions* (***Admin (all permissions)***)
- Click on **Invite user**

```
fastlane run validate_play_store_json_key json_key:./fastlane/pc-api-***.json
```

- Update `./fastline/Appfile`

```
json_key_file("./fastline/pc-api-***.json") # Path to the json secret file - Follow https://docs.fastlane.tools/actions/supply/#setup to get one
package_name("com.adrkacz.reactnativetemplate.app") # e.g. com.krausefx.app
```

## [Add screenshots automation](https://docs.fastlane.tools/getting-started/android/screenshots/)

```
sudo gem install screengrab
```

- Update `android/app/build.gradle`

```
dependencies {
    ...
    androidTestImplementation 'tools.fastlane:screengrab:x.x.x'
}

```

## Setup Supply

### [Generate and upload key and keystore](https://developer.android.com/studio/publish/app-signing#generate-key)

- Open to Android Studio
- Open Projet
- Click on **Build** > **Generate Signed Bundle / APK**
- Select **Android App Bundle**, and click on **Next**
- Below **Key store path**, click on **Create new**
    - **Key store path** : *android/app/upload-keystore-react-native-template.jks*
        - **Password** : password for your keystore
    - **Alias** : alias for your key (*upload-react-native-template*)
    - **Password** : password for you key, *same as password for the keystore*
- Click on **OK**

- Create `~/.gradle/gradle.properties`

```
MYAPP_UPLOAD_STORE_FILE=upload-keystore-react-native-template.keystore
MYAPP_UPLOAD_KEY_ALIAS=upload-react-native-template
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

- Update `android/app/build.gradle`

```
...
android {
    ...
    applicationId "com.adrkacz.reactnativetemplate.app" # or whatever you want
    defaultConfig { ... }
    signingConfigs {
        debug { ... }
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        debug { ... }
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```

### Release a first version of the App

```
./gradlew bundleRelease
```

- Open Google Play Console
- Go to **All Apps**
- Click on **Create app**
    - App Name *React Native Template*
    - Default Language : *English (United Kingdom) - en-GB*
    - App or Game : *App*
    - Free or Paid : *Free*
- Click on **Start testing now** > **Create new release**
- **App integrity** > **Releases signed by Google Play**
- Click on **Upload**
- Select `android/app/build/outputs/bundle/release/app-release.aab`
- Click on **Upload**
- Write a **Release name**, and write quick **Release notes**
- Click on **Save**
- Click on **Review release**
- Click on **Start rollout to internal testing**
- Click on **Rollout**

### Make release available to internal testers

- Go to **Testers** tab
- Click on **Create email list**
    - Add a **List name**
    - Add email addressed and press `Enter`
    - Click on **Save changes**
- Check you email list
- Click on **Save changes**
- Click on **Copy link**, and share it

### Upload to production

- Open Google Play Console
- Go to **Production**
- Click on **Add release**
- Click on **Add from library**, select your build
- Click on **Save**
- Click on **Review release**
- Correct errors

```
fastlane supply init
```


