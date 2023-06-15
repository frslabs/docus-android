# DOCUS ANDROID SDK

![version](https://img.shields.io/badge/version-v1.2.1-blue)

The Docus SDK is a scanner to scan any document. The scanner detects for edges and provides options to crop and rotate images before saving. The scanner allows for single or multiple page scanning and collects them as a single file.

‼ ATTENTION ‼ → BREAKING CHANGE introduced at Docus SDK `v1.2.0`. We have introduced a new license format. If you are using versions prior to `v1.2.0` and intend to update to `v1.2.0`, contact `support@frslabs.com` for an updated license.

# Table Of Content

- [Prerequisite](#prerequisite)
- [Android SDK Requirements](#android-sdk-requirements)
- [Download](#download)
  - [Using maven repository](#using-maven-repository)
- [Setup](#setup)
  - [Permissions](#permissions)
- [Quick Start](#quick-start)
  - [Initiating the Docus scanner](#initiating-the-docus-scanner)
  - [Handling the result](#handling-the-result)
- [Docus Result](#docus-result)
- [Docus Error Codes](#docus-error-codes)
- [Docus Parameters](#docus-parameters)
- [Help](#help)

## Prerequisite

You will need a valid license to use the DOCUS SDK, which can be obtained by contacting `support@frslabs.com` . 

Once you have the license , follow the below instructions for a successful integration of DOCUS SDK onto your Android Application.

## Android SDK Requirements

**Minimum SDK Version** -  **21** or higher

## Download

#### Using maven repository

Add the following code to your `project` level `build.gradle` file

```groovy
allprojects { 
    repositories { 
        maven { 
            // Maven Url and Credentials for Docus SDK. 
            url "https://docus-android.repo.frslabs.space"                 
            credentials { 
                   username 'repo-username' 
                   password 'repo-password' 
            }
       }
    }
}
```

After that, add the following code to your `app` level `build.gradle` file

```groovy
// ...

defaultConfig { 

    // ...
    
    ndk { 
        abiFilters "armeabi-v7a", "arm64-v8a", "x86", "x86_64" 
    } 

    vectorDrawables.useSupportLibrary true 
}

// ...
```

And then, add the dependencies
```groovy

// ...

dependencies {
    /* Dependencies for Docus SDK */ 
    implementation 'com.google.android.material:material:1.2.1'
    implementation 'androidx.appcompat:appcompat:1.2.0'
    implementation "androidx.exifinterface:exifinterface:1.2.0"
    implementation 'androidx.constraintlayout:constraintlayout:2.0.1'
    implementation "androidx.cardview:cardview:1.0.0"
   
    implementation 'com.frslabs.android.sdk:docus:1.2.0'
    implementation "androidx.exifinterface:exifinterface:1.2.0"
    implementation 'com.davemorrissey.labs:subsampling-scale-image-view:3.10.0'
    
    implementation 'androidx.localbroadcastmanager:localbroadcastmanager:1.0.0'
    implementation 'com.google.code.gson:gson:2.8.6'
}
```

## Setup

#### Permissions

Dcous requires the camera permission to work properly

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="your.package.name" >

    <!-- Required by Docus -->
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application>
      ...
    </application>

</manifest>
```

## Quick Start

#### Initiating the Docus scanner

Initialize the `Docus` instance with the appropriate configurations to invoke the Docus Sdk

```java
   
      DocusConfig captusConfig = new DocusConfig.Builder()
                .setLicenseKey(docusLicenseKey)
                .setScanWithFlash(DocUtility.STATUS_NO)
                .setLanguage(DocUtility.EN)
                .setDocumentPageLimit(5)
                .setDocIdOrientation(DocUtility.ANY)
                .setShowInstruction(DocUtility.STATUS_NO)
                .setThemeColor("#2196f3")
                .setDocusRenameFile(DocUtility.STATUS_NO)
                .setScanFeedBack(DocUtility.VIBRATION)
                .setScanMode(DocUtility.MANUAL)
                .build();

        Docus docus = new Docus(captusConfig);
        docus.start(this, new DocusResultCallback() {
            @Override
            public void onDocusSuccess(Document documents) {
                if (null != documents) {
                    Log.e(TAG,"SDK RESULT: "+ documents.toString());
                    Log.d(TAG, "Title: "+ documents.getDocumentTitle());
                    ArrayList<String> strList = documents.getFilePaths();
                    Log.e(TAG, "onActivityResult: Document Name: "+ documents.getTitle());
                    Log.e(TAG, "onActivityResult: Document Images: "+ Arrays.asList(strList));
                    imageModelArrayList = new ArrayList<>();
                    imageModelArrayList = populateList(strList);
                    init(documents.getDocumentTitle());                
                }
                else {
                    Log.e(TAG, "the data is empty.... ");
                }
            }

            @Override
            public void onDocusFailure(int errorCode) {
                Log.e(TAG, "Error: Scan Error Code "+errorCode);
                Toast.makeText(MainActivity.this, "Error: "+ errorCode, Toast.LENGTH_SHORT).show();
            }
        });

}
```


#### Handling the result

Your activity must implement `DocusResultCallback` to receive the result.

```java
           @Override
            public void onDocusSuccess(Document documents) {
                if (null != documents) {
                    Log.e(TAG,"SDK RESULT: "+ documents.toString());
                    Log.d(TAG, "Title: "+ documents.getDocumentTitle());
                    ArrayList<String> strList = documents.getFilePaths();
                    Log.e(TAG, "onActivityResult: Document Name: "+ documents.getTitle());
                    Log.e(TAG, "onActivityResult: Document Images: "+ Arrays.asList(strList));
                    imageModelArrayList = new ArrayList<>();
                    imageModelArrayList = populateList(strList);
                    init(documents.getDocumentTitle());                
                }
                else {
                    Log.e(TAG, "the data is empty.... ");
                }
            }

            @Override
            public void onDocusFailure(int errorCode) {
                Log.e(TAG, "Error: Scan Error Code "+errorCode);
                Toast.makeText(MainActivity.this, "Error: "+ errorCode, Toast.LENGTH_SHORT).show();
            }
        });
        
```

## Docus Error Codes

Following error codes will be returned on the `onDocusFailure` method of the callback

| CODE | DESCRIPTION                  |
| ---- | ---------------------------- |
| 803  | Camera permission denied    |
| 804  | Docus interrupted            |
| 805  | Docus SDK License has expired             |
| 806  | Docus SDK License is invalid             |
| 901  | Network error           |
| 902  | Image upload failed             |
| 1001  | Error parsing result             |

## Docus Parameters

- `setLicenseKey(String docusLicenseKey)`   ***(Required)***
  
  Accepts the Docus SDK licence key as a `String`

  
- `setScanWithFlash(DocUtility.STATUS_NO)`   *(Defaults to **DocUtility.STATUS_NO**)*
  
  Sets scan with flash option or not.


- `setLanguage(DocUtility.EN)`   *(Defaults to **DocUtility.EN**)*
  
  Sets the Docus SDK language - `English` as default.
 
  
- `setDocumentPageLimit(maxPages)`

 sets document scan page limit.

- `setDocIdOrientation(DocUtility.ANY)` 

  Sets the Docus SDK orientation mode.
  
  | Value                   | Effect                                               |
  | ----------------------- | ---------------------------------------------------- |
  | ANY (Recommended)       | capture image with all angles |
  | FLAT       | Captures with the camera that should be flat to the Documents.|
  
- `setShowInstruction(showInstructionStatus)`  *(Defaults to **DocUtility.ANY**)*
  
  | Value                   | Effect                                               |
  | ----------------------- | ---------------------------------------------------- |
  | DocUtility.STATUS_YES     | SDK start with instruction page|
  | DocUtility.STATUS_NO       | SDK without instruction page |  
  
- `setThemeColor(themeColor)`   ***(Optional)***
 
  Sets theme color for SDK, it will reflect in some basic UI of SDK. 
  
  Example Hex code - #2874A6  
  
- `setDocusRenameFile(DocUtility.STATUS_NO)`

  Sets the Docus SDK rename mode.
  
  | Value                   | Effect                                               |
  | ----------------------- | ---------------------------------------------------- |
  | DocUtility.STATUS_YES     | Provide option to rename documents name after scanning|
  | DocUtility.STATUS_NO       | sets automatic deafult documents name  | 
  

- `setScanFeedBack(DocUtility.VIBRATION)`

  Sets the Docus SDK feedback mode.
  
  | Value                   | Effect                                               |
  | ----------------------- | ---------------------------------------------------- |
  | DocUtility.BEEP       | sets beep sound after scan  |
  | DocUtility.VIBRATION     | sets vibration after scan|
  | DocUtility.BOTH       | sets both sound and vibration  |
  | DocUtility.NO_FEEDBACK       | sets feedback as silent mode after scan |
  
  
  
- `setScanMode(scanMode)`

  Sets the Docus SDK SCAN mode.
  
  | Value                   | Effect                                               |
  | ----------------------- | ---------------------------------------------------- |
  | DocUtility.MANUAL     | Click capture button to start scan |
  | DocUtility.AUTO       | Start automatic scan as SDK starts -- future release|  
    
  
## Help
For any queries/feedback , contact us at `support@frslabs.com` 


