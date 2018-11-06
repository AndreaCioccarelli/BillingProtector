# BillingProtector
[![Latest Release](https://jitpack.io/v/AndreaCioccarelli/BillingProtector.svg)](https://jitpack.io/#AndreaCioccarelli/BillingProtector)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/a5bcdb5592d042f1825457fb9fafb778)](https://www.codacy.com/app/cioccarelliandrea01/BillingProtector)
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-BillingProtector-green.svg?style=flat)](https://android-arsenal.com/details/1/7289)
[![Language](https://img.shields.io/badge/language-kotlin-orange.svg)](https://github.com/AndreaCioccarelli/LogKit/blob/master/library/build.gradle)
[![Min sdk](https://img.shields.io/badge/minsdk-14-yellow.svg)](https://github.com/AndreaCioccarelli/LogKit/blob/master/library/build.gradle)
[![License](https://img.shields.io/hexpm/l/plug.svg)](https://github.com/AndreaCioccarelli/BillingProtector/blob/master/LICENSE)

BillingProtector is a robust and tiny library for Android, entirely written in Kotlin. Its main purpose is to check the end-user device state & to secure purchases. 
Its main purpose is to block a transition process if the application has been modified or patched, if the device environment is corrupted or if other suspicious condition is detected; it can also be used to prevent applications from being executed on an unprotected/insecure operative system, like Snapchat does for rooted devices.

## Backgrounding
Hacking android applications is way easier than how it is thought to be. Softwares like [Lucky Patcher](https://www.luckypatchers.com) are phenomenally written to bypass all your first-line defenses and to edit your app executable code, redirecting your purchases to the pirate app and not to the Google Play Store.
That's a violation of the Developer's product and business, since it won't just make you lose money but also users and credibility.
I've been implmenting by myself on every app of mine a complex and different security scheme each time. Then I decided to put everything together and to realize this project of crucial importance for someone's business, in parallel with [CryptoPrefs](https://github.com/AndreaCioccarelli/CryptoPrefs).
Remember that a skilled hacker will always find a way to crack your code. This library is a barrier that will stop the 99% of the other kiddies.

# Setup
BillingProtector uses jitpack as packages repository.
To use it you need to add the repository to your project build.gradle file:
```gradle
allprojects {
    repositories {
        maven { url 'https://jitpack.io' }
    }
}
```
And the dependency to your module build.gradle file:
```gradle
dependencies {
    implementation 'com.github.AndreaCioccarelli:BillingProtector:1.0.2'
}
```

# Usage
BillingProtector has different functionalities and can be used for different purposes.
- Check for pirate applications on the end-user device
- List the found threads on the end-user device
- Determine if the device has root access without requesting it
- Determine root binary path in the operative system

## Initialization
Create an instance of the BillingProtector class passing as argument the context of your current application/activity
```kotlin
val bp = BillingProtector(context)
```

## Check Root
```kotlin
if (bp.isRootInstalled()) {
    finish();
}
```

The method `isRootInstalled()` is going to execute a built-in unix program to determine if the binary `su`, the root permission controller, is present on the system and it will analyze the path of that binary. From this evaluation the result is returned under boolean type.
This method is safe at 100% for 3 reasons. 
- **It's not thread-blocking**, because it executes as less shell commands as possible 
- **It doesn't require root permissions**. Most of the root-checking software analyzes root access presence by actually executing `su` and analyzing the command stdout and stderr. This is slow, unefficient, thread-blocking, it requires your app to ask for root permissions and, in some cases of incompatibility / broken installation, it will freeze your app causing an ANR.
- **The result is reliable**. Many root managers I've had the opportunity to study while building [Impactor](https://play.google.com/store/apps/details?id=com.andreacioccarelli.impactor) places the root binary in hidden directories, under different partitions and among other equally-named programs. This is messy and it can lead to wrong results, basing on the installation type, on the root manager internal mechanism, on the root installation type, and so on and so forth. This tiny trick is efficient and reliable, since it will always just pick the system-linked `su` command


## Check if pirate apps are installed
```kotlin
if (bp.arePirateAppsInstalled()) {
    for (app in bp.getPirateAppsList()) {
        toast(app.packageName)
    }
    finish()
}
```

## Get root binary path
```kotlin
toast(bp.getRootBinatyPath())
```
