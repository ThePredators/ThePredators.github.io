---
title: ⭐ Setup Fastlane + TestFlight + AppCenter for Android/iOS & Flutter
author:
  name: Samy Benghalem
  link: https://github.com/ThePredators
date: 2022-02-24
categories: [Mobile]
tags: [Android, iOS, Flutter]
math: true
mermaid: true
---

## AppCenter data for Fastlane 
For example in this url : https://appcenter.ms/orgs/ToTo/applications 
- owner_name : ToTo
- app_name: Your AppName 
- app_platform: iOS (Obj-c/swift)  / Android (Java)
- api_token: Go to https://appcenter.ms/orgs/YOUR_ORG/apps/YOUR_APP_NAME/settings/apitokens & generate one !

You are ready !

## iOS 

### Generate necessary stuff in Apple Developer Portal

#### Generate AppId
- Go to [Identifiers](https://developer.apple.com/account/resources/identifiers/add/bundleId)
- Select : AppIds
- Select Type : App
- Fill Description : AppName
- Fill Bundle ID : (ex. com.example.toto)
- Choose App Capabilities (can be edited after)
- Then click Register

#### Generate Distribution Certificat
- Go to [Certificates](https://developer.apple.com/account/resources/certificates/add)
- Choose : iOS Distribution (App Store and Ad Hoc)
- Optionnal : Generate a [CSR](https://help.apple.com/developer-account/#/devbfa00fef7)
- Choose the generated CSR 
- Click Validate
- Click download to download certificat 
- Double click on the certificate to install it on your machine

#### Generate Provisionning Profile
- Go to [Profiles](https://developer.apple.com/account/resources/profiles/add)
- Choose : AppStore
- Select AppId : (ex. com.example.toto)
- Choose distribution certificate
- Fill Provisionning Profile Name : (ex. toto-prod-pp)

### Install Fastlane

If you never used [Bundler install it first](https://bundler.io/)
```sh
gem install bundler
```
then :
```sh
bundle install 
fastlane init
# choose 2 for testflight setup
```
This will create folder fastlane with :
- Fastfile
- Appfile

#### Setup file Fastfile : (in fastlane dir)
```sh
default_platform(:ios)

platform :ios do

  desc "Build & Deploy to TestFlight"
  lane :public do |options|
    
    ## Update pubspec.yaml
    ## Work 50% of the time !!!
    # Update Info plist Version
    # increment_version_number(
    #   version_number: options[:version] # Set a specific version number
    # )

    ## Work 50% of the time !!!
    # auto increment buildNumber
    # build_number = number_of_commits(all: true)
    # increment_build_number(build_number: build_number)

    # run this : chmod +x ./scripts/upgrade_buildNumber.sh
    sh("../scripts/upgrade_buildNumber.sh", options[:version])

    # Update Info plist Bundle ID
    update_app_identifier(
      xcodeproj: "Test.xcodeproj", # Optional path to xcodeproj, will use the first .xcodeproj if not set
      plist_path: "./Test/Info.plist", # Path to info plist file, relative to xcodeproj
      app_identifier: ENV["APP_ID"] # The App Identifier
    )
    
    # Update Info plist AppName
    update_info_plist( # Change the Display Name of your app
      plist_path: "./Test/Info.plist",
      display_name: ENV["APP_NAME"]
    )

    # download and use certificate
    match(type: "appstore", readonly: is_ci)

    # Use gym to archive your app
    gym(
      silent: true,
      output_directory: "./fastlane/builds",
      scheme: ENV["SCHEME"]
    )

    # Use pilot to upload your app to testflight
    pilot(
      app_identifier: ENV["APP_ID"],
      distribute_external: false,
    )
  end

  desc "Build & Zip for Private Store"
  lane :private do |options|
    
    ## some cleanup
    sh "rm -rf builds/**.zip"

    ## Update pubspec.yaml
    ## Work 50% of the time !!!
    # Update Info plist Version
    # increment_version_number(
    #   version_number: options[:version] # Set a specific version number
    # )

    ## Work 50% of the time !!!
    # auto increment buildNumber
    # build_number = number_of_commits(all: true)
    # increment_build_number(build_number: build_number)
    
    # run this : chmod +x ./scripts/upgrade_buildNumber.sh
    sh("../scripts/upgrade_buildNumber.sh", options[:version])

    # Update Info plist Bundle ID
    update_app_identifier(
      xcodeproj: "Test.xcodeproj", # Optional path to xcodeproj, will use the first .xcodeproj if not set
      plist_path: "./Test/Info.plist", # Path to info plist file, relative to xcodeproj
      app_identifier: ENV["APP_ID"] # The App Identifier
    )
    # Update Info plist AppName
    update_info_plist( # Change the Display Name of your app
      plist_path: "./Test/Info.plist",
      display_name: ENV["APP_NAME"]
    )

    # Build Archive
    xcodebuild(
      archive: true,
      archive_path: "./fastlane/builds/Test.xcarchive",
      scheme: ENV["SCHEME"],
      workspace: "Test.xcworkspace",
      build_settings: {
        "CODE_SIGNING_REQUIRED" => "NO",
        "CODE_SIGN_IDENTITY" => "",
        "CODE_SIGN_ENTITLEMENTS" => "", 
        "CODE_SIGNING_ALLOWED" => "NO"
      }
    )

    # Clean Archive
    sh "rm -rf builds/Test.xcarchive/dSYMs/"
    sh "rm -rf builds/Test.xcarchive/SwiftSupport/"
    
    # Zip Archive
    zip(
      path: "./fastlane/builds/Test.xcarchive",
      output_path: "./fastlane/builds/test-"+ options[:version] +".xcarchive.zip"
    )
  end
  
  desc "AppCenter Upload"
  lane :appcenter do |options|
    appcenter_upload(
      api_token: "", # found in settings of user
      owner_name: "", # found in the url : https://appcenter.ms/orgs/<owner_name>/applications 
      owner_type: "organization", # Default is user - set to organization for appcenter organizations
      app_name: options[:app_name], # your app name
      file: "./fastlane/app-release.ipa",
      notify_testers: true # Set to false if you don't want to notify testers of your new release (default: `false`)
    )
  end
end
```

#### Add script : upgrade_buildNumber (in iOS rootDir scripts folder)
```
#!/bin/bash

# Info Plist path
PATH_TO_INFOPLIST="YOUR_PATH"
INFOPLIST=$PATH_TO_INFOPLIST"/Info.plist"

# Type a script or drag a script file from your workspace to insert its path.
buildNumber=$(git rev-list HEAD | wc -l | tr -d ' ')

# Updrage BuildNumber with git build Numbe
oldversion=`/usr/libexec/PlistBuddy -c "Print :CFBundleVersion" "$INFOPLIST"`

## Works 100% : Change BuildNumber
if [ "$buildNumber" != "$oldversion" ] ; then
    /usr/libexec/PlistBuddy -c "Set :CFBundleVersion $buildNumber" "$INFOPLIST"
fi

## Change Version 
/usr/libexec/PlistBuddy -c "Set :CFBundleShortVersionString $1" "$INFOPLIST"
```

#### Setup file : Appfile (in fastlane dir)
```sh
# The bundle identifier of your app
app_identifier "com.exemple.toto"

# Apple Developer Account
apple_dev_portal_id "titi@toto.com"

# App Store Connect Account
itunes_connect_id "titi@toto.com"

# Developer Portal Team ID
## is found in the url : https://developer.apple.com/account/#/membership/<team_id>
team_id ""

# App Store Connect Team ID
## Check Step "Get itc_team_id"
itc_team_id ""

# Env for Pilot
ENV["FASTLANE_USER"] = "titi@toto.com"
ENV["FASTLANE_ITC_TEAM_ID"] = "<itc_team_id>"

## To setup 2 factor Auth for delivery
# Check Step "Get the FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD" 
ENV["FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD"] = ""

# Specify the Trusted phone number to automatize sms verification step 
## https://github.com/fastlane/fastlane/blob/master/spaceship/docs/Authentication.md#auto-select-sms-via-spaceship_2fa_sms_default_phone_number
### Go here and add your phone number as trusted phone, then fill the field : https://appleid.apple.com/
ENV["SPACESHIP_2FA_SMS_DEFAULT_PHONE_NUMBER"] = ""
```

**Get itc_team_id**
```sh
$ irb
irb> require "spaceship"
irb> Spaceship::Tunes.login("iTunesConnect_username", "iTunesConnect_password")
irb> Spaceship::Tunes.select_team
```

Don't forget to replace :
- iTunesConnect_username
- iTunesConnect_password

The result contains the itc_team_id

**Get the FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD**
- Connect to [AppleId](https://appleid.apple.com/account/manage)
- Go to Security -> Generate Password for App -> set AppName -> copy the generated code "....-....-....-...."
- This code is the FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD

#### Setup file : Matchfile (in fastlane dir)
```sh
# To Replace : .git storage for the certificate shared with hole team
git_url("https://toto/titi/tata.git")
storage_mode("git")
# The default type, can be: appstore, adhoc, enterprise or development
type("development")
# Your Apple Developer Portal username
username("toto@tata.com")
```

#### Setup file : Pluginfile (in fastlane dir)
```sh
gem 'fastlane-plugin-appcenter'
```

#### Setup file : Gemfile (in ios rootDir)
```
source "https://rubygems.org"

gem "fastlane"

plugins_path = File.join(File.dirname(__FILE__), 'fastlane', 'Pluginfile')
eval_gemfile(plugins_path) if File.exist?(plugins_path)
```

#### Add file .env.environement : (in fastlane dir)
- (ex. .env.dev for dev)
```
SCHEME="test-dev"
APP_ID="fr.exemple.test"
APP_NAME="Test DEV"
```

### How to Build & Deliver to Tesflight ? 
```
fastlane ios public version:"1.0.0" --env "dev" # ex. for dev
```

## Android 

### Install Fastlane
```sh
fastlane init
# choose 2 for testflight setup
```
This will create folder fastlane with :
- Fastfile
- Appfile

#### Setup file : Fastfile (in fastlane dir)
```sh
default_platform(:android)

platform :android do  
  desc "Deploy a new version to the Google Play"
  lane :deploy do |options|
    flutter_build
    ## To use this u need to generate : api.json ! 
    ## Check the Appfile
    upload_to_play_store(track: 'beta') 
  end
  
  desc "Deploy a new version to AppCenter"
  lane :appcenter_bad do |options|
    flutter_build
    appcenter
  end

  desc "Build with fastlane with auto upgrade VersionCode"
  lane :flutter_build do
    # Return the number of commits in current git branch
    build_number = number_of_commits()
    Dir.chdir ".." do
      sh("flutter", "packages", "get")
      sh("flutter", "clean")
      # sh("flutter", "build", "apk", "--build-number=#{build_number}")
      # sh("flutter", "build", "appbundle", "--build-number=#{build_number}")
      ## TODO : Build for different BuildTypes ! 
      sh("flutter", "build", "apk", "--release")
    end
  end

  desc "AppCenter Upload"
  lane :appcenter do |options|
    appcenter_upload(
      api_token: "", # set api Token from appcenter
      owner_name: "", # Set App owner name
      owner_type: "organization", # Default is user - set to organization for appcenter organizations
      app_name: "", # your app name
      file: "../build/app/outputs/flutter-apk/app-release.apk",
      notify_testers: true,
      app_platform: 'Java',
      destinations: "", # Distribution group
      destination_type: "group"
    )
  end
  
end
```

#### Setup file : Appfile (in fastlane dir)
```sh
json_key_file("") # Path to the json secret file - Follow https://docs.fastlane.tools/actions/supply/#setup to get one
package_name("com.krausefx.app") # e.g. 
```

#### Setup file : Pluginfile (in fastlane dir)
```sh
gem 'fastlane-plugin-appcenter'
```

#### Setup file : Gemfile (in android rootDir)
```
source "https://rubygems.org"

gem "fastlane"

plugins_path = File.join(File.dirname(__FILE__), 'fastlane', 'Pluginfile')
eval_gemfile(plugins_path) if File.exist?(plugins_path)

```

### How to Build & Deliver to AppCenter ? 
```
## To upgrade the version edit pubspec.yaml
fastlane android appcenter_bad
```

## .gitignore

### iOS .gitignore
```sh
*.mode1v3
*.mode2v3
*.moved-aside
*.pbxuser
*.perspectivev3
**/*sync/
.sconsign.dblite
.tags*
**/.vagrant/
**/DerivedData/
Icon?
**/Pods/
**/.symlinks/
profile
xcuserdata
**/.generated/
Flutter/App.framework
Flutter/Flutter.framework
Flutter/Flutter.podspec
Flutter/Generated.xcconfig
Flutter/app.flx
Flutter/app.zip
Flutter/flutter_assets/
Flutter/flutter_export_environment.sh
ServiceDefinitions.json
Runner/GeneratedPluginRegistrant.*

# Exceptions to above rules.
!default.mode1v3
!default.mode2v3
!default.pbxuser
!default.perspectivev3

.DS_Store
.Trashes
*.swp
*.lock
*~.nib
buildArchive/
DerivedData/
build/
*.pbxuser
*.mode1v3
*.mode2v3
*.perspectivev3
!default.pbxuser
!default.mode1v3
!default.mode2v3
!default.perspectivev3
*.hmap
*.ipa
*.dSYM.zip
*.dSYM
timeline.xctimeline
playground.xcworkspace
*.xccheckout
xcuserdata/
*.moved-aside
.build/
Pods/
Carthage/Checkouts
Carthage/Build
fastlane/report.xml
fastlane/Preview.html
fastlane/screenshots/**
fastlane/screenshots/**/*.png
fastlane/test_output
*.mobileprovision
*.cer
fastlane/*.cer
fastlane/*.mobileprovision
fastlane/builds/**
.history/**
*.p12
*.certSigningRequest
*.pdf
Mocks/**
**/android/.gradle
**/android/captures/
**/android/local.properties
**/android/**/GeneratedPluginRegistrant.java
```
### Android .gitignore
```sh
gradle-wrapper.jar
/.gradle
/captures/
/gradlew
/gradlew.bat
/local.properties
GeneratedPluginRegistrant.java
```

### Flutter RootProj .gitignore
```sh
# Miscellaneous
*.class
*.lock
*.log
*.pyc
*.swp
.DS_Store
.atom/
.buildlog/
.history
.svn/

# IntelliJ related
*.iml
*.ipr
*.iws
.idea/

# Visual Studio Code related
.classpath
.project
.settings/
# .vscode/

# Flutter repo-specific
/bin/cache/
/bin/mingit/
/dev/benchmarks/mega_gallery/
/dev/bots/.recipe_deps
/dev/bots/android_tools/
/dev/docs/doc/
/dev/docs/flutter.docs.zip
/dev/docs/lib/
/dev/docs/pubspec.yaml
/dev/integration_tests/**/xcuserdata
/dev/integration_tests/**/Pods
/packages/flutter/coverage/
version

# packages file containing multi-root paths
.packages.generated

# Flutter/Dart/Pub related
**/doc/api/
.dart_tool/
.flutter-plugins
.flutter-plugins-dependencies
.packages
.pub-cache/
.pub/
build/
flutter_*.png
linked_*.ds
unlinked.ds
unlinked_spec.ds

# Android related
**/android/**/gradle-wrapper.jar
**/android/.gradle
**/android/captures/
**/android/gradlew
**/android/gradlew.bat
**/android/local.properties
**/android/**/GeneratedPluginRegistrant.java
**/android/key.properties
*.jks

# iOS/XCode related
**/ios/**/*.mode1v3
**/ios/**/*.mode2v3
**/ios/**/*.moved-aside
**/ios/**/*.pbxuser
**/ios/**/*.perspectivev3
**/ios/**/*sync/
**/ios/**/.sconsign.dblite
**/ios/**/.tags*
**/ios/**/.vagrant/
**/ios/**/DerivedData/
**/ios/**/Icon?
**/ios/**/Pods/
**/ios/**/.symlinks/
**/ios/**/profile
**/ios/**/xcuserdata
**/ios/.generated/
**/ios/Flutter/App.framework
**/ios/Flutter/Flutter.framework
**/ios/Flutter/Flutter.podspec
**/ios/Flutter/Generated.xcconfig
**/ios/Flutter/app.flx
**/ios/Flutter/app.zip
**/ios/Flutter/flutter_assets/
**/ios/Flutter/flutter_export_environment.sh
**/ios/ServiceDefinitions.json
**/ios/Runner/GeneratedPluginRegistrant.*

# macOS
**/macos/Flutter/GeneratedPluginRegistrant.swift
**/macos/Flutter/Flutter-Debug.xcconfig
**/macos/Flutter/Flutter-Release.xcconfig
**/macos/Flutter/Flutter-Profile.xcconfig

# Coverage
coverage/

# Symbols
app.*.symbols

# Exceptions to above rules.
!**/ios/**/default.mode1v3
!**/ios/**/default.mode2v3
!**/ios/**/default.pbxuser
!**/ios/**/default.perspectivev3
!/packages/flutter_tools/test/data/dart_dependencies_test/**/.packages
!/dev/ci/**/Gemfile.lock

doc/*

.idea/*
#.env*
```

## More Infos :
- [Apple Program Roles](https://developer.apple.com/support/roles/)
- [AppStoreConnect](https://appstoreconnect.apple.com/)
- [DeveloperPortal](https://developer.apple.com/account/)