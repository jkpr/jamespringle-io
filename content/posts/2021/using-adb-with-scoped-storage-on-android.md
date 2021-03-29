+++
title = "Using ADB to get to an app's scoped storage on Android"
date = "2021-03-29T13:03:16-05:00"
author = "James K. Pringle"
authorTwitter = "" #do not include @
cover = ""
tags = ["android", "adb"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

Android has recently introduced [scoped storage][scoped-storage] to the platform, and accessing app-specific directories is a little more difficult.

[scoped-storage]: https://developer.android.com/training/data-storage#scoped-storage

{{< image src="/static/img/2021/android-debugging.jpg" alt="Debugging an Android device" position="center" style="border-radius: 8px;" >}}

I work with [ODK Collect][collect], an app that has been with Android since version API level 3, version 1.5, Android Cupcake. Nowadays, Android is on API level 30 and version 11. (See more information at [Android build numbers][android-builds].)

[collect]: https://github.com/getodk/collect
[android-builds]: https://source.android.com/setup/start/build-numbers

Since ODK Collect has been around so long, it has always stored its information in a directory called `odk` that is in common storage directory alongside other important Android folders such as `Alarms`, `Download`, and `Movies`. Android used to be the Wild West when it comes to storage directories.

Now that scoped storage is becoming the only option for external storage, all those files that were easy to access at `/sdcard/odk` now require a little more work to get to.

ODK Collect's new path for scoped storage is:

```
/storage/emulated/0/Android/data/org.odk.collect.android/files
```

The Android Studio [device file explorer][explorer] can access these files, but I found that I had to double click certain directories more than once to get them to expand. It was surprising that I would double click a folder and nothing would happen. But persistence won the day, and double clicking again (and again, if needed) was the solution.

[explorer]: https://developer.android.com/studio/debug/device-file-explorer

The device file explorer uses [`adb`][adb] under the hood. This is a tool that can be used on the command line, too. However, using `adb` on the command line did not work right away either.

[adb]: https://developer.android.com/studio/command-line/adb

{{< image src="/static/img/2021/adb-non-root.png" alt="Run standard ADB and have no permissions to scoped storage" position="center" style="border-radius: 8px;" >}}

I had to restart `adb` as root and then I could get to the scoped storage files.

{{< image src="/static/img/2021/adb-root.png" alt="Run ADB as root and have permissions to scoped storage" position="center" style="border-radius: 8px;" >}}

With the introduction of scoped storage, the files are still there, but they are a little harder to get to. Scoped storage can be accessed with both the device file explorer in Android Studio and with `adb` on the command line.