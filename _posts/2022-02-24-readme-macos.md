---
title: 🔥 macOS (CheatSheet)
author:
  name: Samy Benghalem
  link: https://github.com/ThePredators
date: 2022-02-24
categories: [Apple, macOS]
tags: [macOS, Apple]
math: true
mermaid: true
---

## How to use ?

**Enable in Security / Confidentiality the Install From everywhere Button**
```sh
sudo spctl --master-disable
```

**Show Hidden Files**
```sh
defaults write com.apple.finder AppleShowAllFiles -boolean true; killall Finder
```

**Hide Hidden Files**
```sh
defaults write com.apple.finder AppleShowAllFiles -boolean true; killall Finder
```

**Display IP Adress**
```sh
ifconfig | grep "inet " | grep -v 127.0.0.1 | cut -d\  -f2
```

**Flush DNS**
```sh
dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

**Run Software Updates**
```
softwareupdate --all --install --force 
# add this flag to restart computer after installing updates : --restart

## If the above command showed no update
sudo rm -rf /Library/Developer/CommandLineTools
sudo xcode-select --install
```

## Usefull Symboles 
```md
⌘ is the Command () key.
⌃ is the Control key.
⌥ is the Option (alt) key.
⇧ is the Shift key.
⇪ is the Caps Lock key.
```

## 🔥 macOS Apple Store CLI [MAS](https://github.com/mas-cli/mas)

**Install tools**
```sh
brew install mas
```

**Search**
```sh
mas search Spark
```

**Download**
```sh
mas install <ByID>
```

## 🔥 macOS Downloader

This command will allow you to download any macOS installer App ! Just name a version and you are good to go. 

Versions can be found here : [MacOS_version_history](https://en.wikipedia.org/wiki/MacOS_version_history)

**List available installers**
```sh
softwareupdate --list-full-installers
```

**Upgrade command (Only)**
```sh
MACOS_VERSION="12.1" # for Monterey
softwareupdate --fetch-full-installer --full-installer-version $MACOS_VERSION
```

**Make a Bootable IMG**
```sh
sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/UNTITLED --nointeraction
```

**Download all versions**
```sh
... # on Progress
```

Enjoy :) 