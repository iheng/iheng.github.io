---
layout: post
title: Flutter network connection issues
published: true
---
#### Flutter network connection issues(faild host lookup)
I tried to call api from emulator to my host machine and I got following error message
```E/flutter (16241): [ERROR:flutter/lib/ui/ui_dart_state.cc(148)] Unhandled Exception: SocketException: Failed host lookup: 'us1.uszcn.local' (OS Error: No address associated with hostname, errno = 7)```
It took me one hour to figure out what's the issues is. 

#### Step 1: Check whether internet pemisson was granted
Open your AdroidManifest.xml file check whether internet pemisson was granted. if not add following to the file ` <uses-permission android:name="android.permission.INTERNET"/>`

If after edit AdroidManifest.xml not solve your issue. which didb't solve my issue.
please process to step 2:

#### Step2: Check your emulator proxy setting.
According to the offical document. you need to set hostname as 10.0.2.2: port number is whichever you using in your local machine. 


Try again, if you can call you service. or in your emulator,open a webbrowser visit 10.0.2.2:portname(the port you're using in your local machine). if you can visit, that means your proxy setting is working.

if you still can't error messge. look up your host file
in your local machine. you may setting up custom domain for it 
#### step3:Edit your emulator host file.
open a terminal and change your directory to /Library/Android/sdk/
default image is read only. so you have to change it to writeble. under sdk directory change to tools directory 
type command ` ./emulator -avd huawei_T3_10_API_24 -writable-system`
this will enable you copy your local host file to emulator. 
open another terminal. under /Library/Android/sdk/platorm_tools directory,type command: `./adb root && ./adb -s emulator-5554 remount && ./adb -s <your_device_name> push /etc/hosts /system/etc/hosts`
if your don't what your device_name is. type './adb devices' in your terminal. You would see all your devices. This command will copy your local machine host file to emulator. 

now, you can check whether it successfully push to your emulator. type `./adb shell` in your terminal, it will lunch a shell. type `cat etc/host` you will see your host from your emulator. 

References:

- [How to edit /etc/hosts file in Android Studio emulator running in nougat?](https://stackoverflow.com/questions/41117715/how-to-edit-etc-hosts-file-in-android-studio-emulator-running-in-nougat)
- [Set up Android Emulator networking](https://developer.android.com/studio/run/emulator-networking)


