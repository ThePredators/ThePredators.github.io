---
title: ⭐ Setup Mobile Development Environment (Tested on macOS M1+Intel)
author:
  name: Samy Benghalem
  link: https://github.com/ThePredators
date: 2022-02-23 19:00:00 +0800
categories: [Mobile]
tags: [Android, iOS, Flutter]
math: true
mermaid: true
---

## Install [HomeBrew](https://brew.sh/)
```sh
## Install xcode utils
xcode-select --install
## Install Brew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
## Check that everything is Ok
brew doctor
## To update HomeBrew
brew update
```

To update/upgrade a brew package just run :
```sh
brew reinstall PACKAGE_NAME
```

**Add HomeBrew to Your Path**
```sh
HBP=`brew --prefix`
export PATH=$HBP/bin:$PATH
```

**Apple M1 Support**

Add this alias to .zshrc
```sh 
alias brew="arch -arm64 brew"
```

## Install [ZSH](https://ohmyz.sh/) 
```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### 🔆 Configure Zsh in a clever way 

Clone the following repository and follow the described steps [zshconfig](https://github.com/ThePredators/zshconfig)

(The above tool is fully automated and available for Intel & M1 Mac)

### Install PowerLvl Template + Zsh Plugins 
- [Powerlevel9k](https://medium.com/@Clovis_app/configuration-of-a-beautiful-efficient-terminal-and-prompt-on-osx-in-7-minutes-827c29391961)
- [Powerlevel10k (Recommended)](https://gist.github.com/kevin-smets/8568070)

```sh
## Install Power10k :
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
### change ZSH_THEME to : ZSH_THEME="powerlevel10k/powerlevel10k"
### then run :
p10k configure

## Or

## Install Power9k :
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
### change them in .zshrc  : ZSH_THEME="powerlevel9k/powerlevel9k"

## Install Auto-Suggestion :
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
### Edit .zshrc & add in plugins array : plugins=(zsh-autosuggestions)

## Install Syntaxe Hylight :
brew install zsh-syntax-highlighting
## Copy/Paste in .zshrc before rvm Config : source `brew --prefix`/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh >> /Users/$(whoami)/.zshrc

## Install Completion : 
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
### Edit .zshrc & add in plugins : plugins=(zsh-autosuggestions)
```

Don't forget to import Meslo Font into iTerm -> Preferences -> Profiles -> "Selected Profile" -> Text -> Font -> Select the [Meslo](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k) font you want to use. 

## Install [Git](https://git-scm.com/downloads)
```sh
brew install git
brew install git-lfs
```

## Install [Git Flow](https://danielkummer.github.io/git-flow-cheatsheet/)
```sh
brew install git-flow
```

## Install [Sublime Text](https://www.sublimetext.com/) 
```sh
brew install --cask sublime-text
```

## Install [Fork (Recommended)](https://git-fork.com/)
```sh
brew install --cask fork
```

## Install [iTerm](https://iterm2.com/)
```sh
brew install --cask iterm2
```

### Install PowerLine Fonts for iTerm (Optionnal) :

```sh
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts && ./install.sh
# clean-up a bit
cd .. && rm -rf fonts
```

## Install Ruby 

**Recommended using : [rbenv](https://github.com/rbenv/rbenv)**

### Globally :

1. Install and configure rbenv
```sh
brew install rbenv
```
then add this 2 line to .zshrc or .bashrc
```sh
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```
2. Install ruby version 2.7.4 
```sh 
rbenv install 2.7.4
```
3. Set Global ruby version
```sh
rbenv global 2.7.4
```
4. Install latest version of bundler
```sh
gem install bundler:2.2.19 -n /usr/local/bin
```

### Under you project :

**Set local ruby version**
```sh
rbenv local 2.7.4
```

**If you need to run `bundle install` you need to run instead this :**
```sh
bundle install --path vendor/bundle
```

### M1 Support

Add the following to your .zshrc
```sh
alias gem="arch -x86_64 sudo gem"
```

Please run the following scripts too !
```sh
gem install --user-install ffi -- --enable-libffi-alloc
```

**More Options :**
- [using rvm](https://gist.github.com/ThePredators/63548e82749418aa805885c1ec87b97f)


## Install [CocoaPods](https://guides.cocoapods.org/using/getting-started.html) :
```sh
sudo gem install cocoapods -n /usr/local/bin
```

**M1 Support**

Add this to .zshrc
```sh
alias pod="arch -arm64 pod"
```

## Install [Swift Package Manager (Recommended)](https://swift.org/getting-started/)

No Need to install on macOS ! Don't forget to check the SPM Aliases !

- **SPM Template (With SwiftUI) : [swift5-module-template](https://github.com/fulldecent/swift5-module-template)**
- **SPM Sample App (With StoryBoard) : [SwiftExampleAppWithSPM](https://github.com/ThePredators/SwiftExampleAppWithSPM)**

## Install XCode 

Many options to download Xcode you can use the following

**Recommended**
Using : [Xcodes](https://github.com/RobotsAndPencils/xcodes)

```sh
# Install xcodes
brew install robotsandpencils/made/xcodes

# list available xcode versions
xcodes list

# Install XCode
xcodes install 13 Beta 5
```

Before running xcodes, export this environment variables :
```sh
export XCODES_USERNAME=""
export XCODES_PASSWORD=""
```

**Others :**
- [MAS](https://github.com/mas-cli/mas)
- [Xcodes BigSure+](https://github.com/RobotsAndPencils/XcodesApp)
- [Downloader-for-Apple-Developers](https://github.com/vineetchoudhary/Downloader-for-Apple-Developers)
- [Troubleshooting](https://wilsonmar.github.io/xcode/#XcodeInstall)

## Install iOS Simulator :

**Install xcode-install**
```sh
sudo gem install xcode-install
```

**Display all simulators By name**
```sh
xcversion simulators
```

**Install a Simulator**
```sh
xcversion simulators --install="iOS 14.4 Simulator"
```

**Display all availables devices and simulators ByIds**
```sh
xcrun simctl list
```

Output Example :
```md
D9C60396-FC76-4693-AEE2-84AA7C8EB200 (active, disconnected)
    Watch: Apple Watch Series 6 - 44mm (FE1A98F8-C2F2-446D-B436-B484B1D87837) (Shutdown)
    Phone: iPhone 12 Pro Max (7395B23C-9AAE-4CEB-8FCC-82A70FCE0553) (Shutdown)
```

**Open Simualtor/Device ById**

I advice to add this alias to your config :
```sh
alias sim="/Applications/Xcode.app/Contents/Developer/Applications/Simulator.app/Contents/MacOS/Simulator -CurrentDeviceUDID"
```
Then just run :
```sh
sim D9C60396-FC76-4693-AEE2-84AA7C8EB200
```
(ex. From above the DeviceUDID is : D9C60396-FC76-4693-AEE2-84AA7C8EB200)

**To Open latest iOS Simulator**
```sh
open -a Simulator
```

## Install Java JDK :

**Using [Jabba "Java Version Manager"](https://github.com/shyiko/jabba) (Recommanded)**
```sh
# Install Jabba 
echo $'\nnexport JABBA_VERSION="0.11.2"\n' >> /Users/$(whoami)/.zshrc
curl -sL https://github.com/shyiko/jabba/raw/master/install.sh | bash && . ~/.jabba/jabba.sh

# Verify this line is in .zshrc (Should be added automatically)
[ -s "/Users/$(whoami)/.jabba/jabba.sh" ] && source "/Users/$(whoami)/.jabba/jabba.sh"

# List available java versions
jabba ls-remote

# Install JDK 16
jabba install openjdk@1.16.0

# Verify java version
java -version

## The result should be : 
# openjdk 16.0.1 2021-04-20
# OpenJDK Runtime Environment (build 16.0.1+9-24)
# OpenJDK 64-Bit Server VM (build 16.0.1+9-24, mixed mode, sharing)

# Switch to a different Java version (need to first be installed using Jabba)
jabba use ...

# Don't forget to export JAVA_HOME
echo $"\nexport JAVA_HOME=/Users/$(whoami)/.jabba/jdk/openjdk@1.16.0/Contents/Home\n" >> /Users/$(whoami)/.zshrc
echo $"\nexport PATH=$JAVA_HOME:$PATH\n" >> /Users/$(whoami)/.zshrc
```

**Old way :**
```sh
brew tap adoptopenjdk/openjdk
brew install --cask adoptopenjdk8
echo $"\nexport JAVA_HOME=/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home/\n" >> /Users/$(whoami)/.zshrc
echo $"\nexport PATH=$JAVA_HOME:$PATH\n" >> /Users/$(whoami)/.zshrc
```

## Install Gradle 

```sh
## In my case latest version of gradle was : 7.3.3
brew install gradle
echo $"\nexport GRADLE_HOME=/usr/local/Cellar/gradle/7.3.3\n" >> /Users/$(whoami)/.zshrc
echo $"\nexport PATH=$GRADLE_HOME/bin:$PATH\n" >> /Users/$(whoami)/.zshrc
```

## Install Android Studio + SDK + NDK

**Install Android studio**
```sh
brew install --cask android-studio
```

**Install Android SDK (sdkmanager, avdmanager, lint, retrace, screenshot2, apkanalyzer)**
```sh
## Since android-sdk is deprecated
brew install --cask android-commandlinetools

# Export some paths
echo $"\nexport ANDROID_HOME=`brew --prefix`/share/android-commandlinetools\n" >> /Users/$(whoami)/.zshrc
echo $"\nexport PATH=$ANDROID_HOME:$PATH\n" >> /Users/$(whoami)/.zshrc

echo $"\nexport SDK_MANAGER=`brew --prefix`/bin/sdkmanager\n" >> /Users/$(whoami)/.zshrc
echo $"\nexport PATH=$SDK_MANAGER:$PATH\n" >> /Users/$(whoami)/.zshrc

echo $"\nexport AVD=`brew --prefix`/bin/avdmanager\n" >> /Users/$(whoami)/.zshrc
echo $"\nexport PATH=$AVD:$PATH\n" >> /Users/$(whoami)/.zshrc

# Then Install Tools
sdkmanager "patcher;v4"
sdkmanager "build-tools;30.0.2"
sdkmanager "platforms;android-32"
sdkmanager "emulator"
sdkmanager "tools"
sdkmanager "platform-tools"

# Then Accept all licenses
yes | sdkmanager --licenses
``` 

Make sure also that in your Android project folder you have this in local.properties :
```groovy
sdk.dir=/usr/local/share/android-commandlinetools
```

**Install Android NDK**
```sh
sdkmanager "ndk-bundle"
```

**Install (ADB, fastboot, sload_f2fs, dmtracedump, e2fsdroid, etc1tool, hprof-conv, make_f2fs, make_f2fs_casefold, mke2fs)**
```sh
brew install android-platform-tools

echo $"\nexport ADB=`brew --prefix`/bin/adb\n" >> /Users/$(whoami)/.zshrc
echo $"\nexport PATH=$ADB:$PATH\n" >> /Users/$(whoami)/.zshrc
```

More Infos :
- [SDKManager](https://developer.android.com/studio/command-line/sdkmanager)

## Install Android Emulator

**[GenyMotion (Recomended)](https://www.genymotion.com/)**
```sh
brew install --cask genymotion
```

Usefull ClI :
- [genyshell](https://docs.genymotion.com/desktop/latest/05_Genymotion_Shell.html#starting-genymotion-shell)
- [gmtool](https://docs.genymotion.com/desktop/latest/06_GMTool.html#general-commands)
```sh
## Copy this to .zshrc
echo 'GMTOOL_PATH="/Applications/Genymotion.app/Contents/MacOS/"' >> /Users/$(whoami)/.zshrc
echo 'export PATH=$GMTOOL_PATH:$PATH' >> /Users/$(whoami)/.zshrc
## Display devices list
genyshell -c "devices list"

## To Create / run u need licence !
## Create a device
gmtool admin create "Google Pixel - 9.0 - API 28 - 1080x1920" GP_9
## Run a device
gmtool admin run GP_9
```

**Android Emulator - Apple M1**

Download this emulator [Apple M1](https://github.com/google/android-emulator-m1-preview/releases)

## Install Android File Transfer (Optionnal)
```sh
brew install --cask android-file-transfer
```

## Install Vysor (Optionnal)
```sh
brew install --cask vysor
```

## Install [Scrcpy (Recomended)](https://github.com/Genymobile/scrcpy) (Vysor + Android File Transfer alternative)
```sh
brew install scrcpy
```

## Install [Flutter](https://flutter.dev/docs/get-started/install) 
```sh
## Install Flutter
brew install --cask flutter
# Instal dart
brew tap dart-lang/dart
brew install dart
## Migrate to null safety (Only inside a project)
# dart migrate --apply-changes
## Check That everythong is OK
flutter doctor
```

**Extra Steps for Android usage in Flutter**
```sh
flutter config --android-sdk $ANDROID_HOME
sdkmanager --install "cmdline-tools;latest"
# don't forget after each run of sdkmanager --install to run 
yes | sdkmanager --licenses
```

**Apple M1 Support for Flutter & Docker**
```sh
softwareupdate --install-rosetta
## Flutter
arch -x86_64 sudo gem install ffi
```

## Install Docker

Download this docker M1 image [Docker Desktop Apple M1](https://desktop.docker.com/mac/stable/arm64/Docker.dmg?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-mac-arm64)

## Install [Fastlane](https://docs.fastlane.tools/)
```sh
brew install fastlane
```
[**Here is a quick Tutorial on how to integrate Fastlane with TestFlight & AppCenter for Android/iOS/Flutter**](https://gist.github.com/ThePredators/a1d105b0794198cade4ef2b0e0836541)

## Install [VsCode](https://code.visualstudio.com/) 

```sh
brew install --cask visual-studio-code
```

**How to use iTerm + PowerLvl10k in Vscode** : 

(I assume here you already followed steps decribed in section **Install ZSH**)

Edit this Vscode User Settings file `/Users/$(whoami)/Library/Application\ Support/Code/User/settings.json` and add the following :

```json
{
    ...
    "terminal.external.osxExec": "iTerm.app",
    "terminal.integrated.shell.osx": "/bin/zsh",
    "terminal.integrated.fontFamily": "'MesloLGS NF'",
    "terminal.integrated.cursorStyle": "line",
    "terminal.integrated.lineHeight": 1.3
}
```

If for some reason you want to edit those colors you can add this and play a little bit with values : 

```json
{
    ...
    "workbench.colorCustomizations": {
        "terminal.background":"#131212",
        "terminal.foreground":"#6bb8e6",
        "terminal.ansiBlack":"#000000",
        "terminal.ansiBrightBlack":"#414141",
        "terminal.ansiBlue": "#bceafa",
        "terminal.ansiBrightBlue":"#77dcff",
        "terminal.ansiCyan":"#a3e9d4",
        "terminal.ansiBrightCyan":"#a3e9d5",
        "terminal.ansiGreen":"#68f400",
        "terminal.ansiBrightGreen":"#dcf152",
        "terminal.ansiMagenta":"#f58a8d",
        "terminal.ansiBrightMagenta":"#ffb4b8",
        "terminal.ansiRed":"#ff4c41",
        "terminal.ansiBrightRed":"#ff7c77",
        "terminal.ansiWhite":"#ffffff",
        "terminal.ansiBrightWhite":"#ffffff",
        "terminal.ansiYellow":"#ffe100",
        "terminal.ansiBrightYellow":"#fff68b",
    }
}
```

## Install some macOS developer Tools

**[OpenInTerminal](https://github.com/Ji4n1ng/OpenInTerminal)**
```sh
brew install --cask openinterminal
```
**[Spectacle](https://github.com/eczarny/spectacle)**
```sh
brew install --cask spectacle
```
**[Dozer](https://github.com/Mortennn/Dozer)**
```sh
brew install --cask dozer
```
**[DB Browser for SQLite](https://sqlitebrowser.org)**
```sh
brew install --cask db-browser-for-sqlite
```
**[Proxyman](https://proxyman.io/)**
```sh
brew install --cask proxyman
```
**[PostMan](https://www.postman.com)**
```sh
brew install --cask postman
```

## Install Browser Plugins :

**Chrome**
- [Octotree](https://chrome.google.com/webstore/detail/octotree-github-code-tree/bkhaagjahfmjljalopjnoealnfndnagc)
- [User Agent Switcher](https://chrome.google.com/webstore/detail/user-agent-switcher-for-c/djflhoibgkdhkhhcedjiklpkjnoahfmg)
- [DarkReader](https://chrome.google.com/webstore/detail/dark-reader/eimadpbcbfnmbkopoojfekhnkhdbieeh)
- [Session Manager](https://chrome.google.com/webstore/detail/session-buddy/edacconmaakjimmfgnblocblbcdcpbko)

## TroubleShoots :

**Install Rosetta 2**
```sh
softwareupdate --install-rosetta
```

You're good to go !