# react-native-ms-adal

This is a port of the [Active Directory Authentication Library (ADAL) plugin for Apache Cordova apps](https://github.com/AzureAD/azure-activedirectory-library-for-cordova) to work with React Native.

It provides all the basic authentication functions and keychain stuff as per the original cordova library.

Hopefully Microsoft will release an official version soon.

## Prerequisites

1. A working react native project.  Tested on react-native 0.41 and above
2. A working CocoaPods installation [CocoaPods - Getting Started](https://guides.cocoapods.org/using/getting-started.html)

## Installation iOS

1. Install from npm `npm install --save react-native-ms-adal`
2. Add the ADAL ios library to your ios/Podfile file  `pod 'ADAL', '~> 2.3'`.  Create Podfile with `pod init` if required.
3. Run `pod install` to pull the ios ADAL library down.
4. In you react-native project root folder run `react-native link react-native-ms-adal`

## Installation Android

1. Install from npm `npm install --save react-native-ms-adal`
2. In you react-native project root folder run `react-native link react-native-ms-adal`

## Installation Windows UWP (Experimental)

1. Install from npm `npm install --save react-native-ms-adal`
2. Open windows/{projectname}.sln
3. Add '../node_modules/react-native-ms-adal/uwp/ReactNativeMSAdal.csproj' to the solution
4. Add `new ReactNativeMSAdal.ReactNativeMSAdalPackage()` to the packages list in MainPage.cs

__Notes:__ the UWP implementation is a wrapper for WebAuthenticationCoreManager instead of ADAL and has therefore not implenented direct access to the tokenCache since that is not accessible from that API. `MSAdalLogin()` and `MSAdalLogout()` will therefor not work, use the msadal/AuthenticationContext.js directly instead: `import AuthenticationContext from 'react-native-ms-adal/msadal/AuthenticationContext'`

## Installation Windows WPF (Experimental)

1. Install from npm `npm install --save react-native-ms-adal`
2. Open windows/{projectname}.sln
3. Add '../node_modules/react-native-ms-adal/wpf/ReactNativeMSAdal.Net46.csproj' to the solution
4. Add '''new ReactNativeMSAdal.ReactNativeMSAdalPackage()''' to the packages list in MainPage.cs


## Usage Example

See [Active Directory Authentication Library (ADAL) plugin for Apache Cordova apps](https://github.com/AzureAD/azure-activedirectory-library-for-cordova) for details on how to use the AuthenticationContext.  This library renames this to MSAdalAuthenticationContext, which can be imported as follows

```javascript
import {MSAdalAuthenticationContext} from "react-native-ms-adal";
```

There are also couple of promised based utility functions to provide login and logout functionality. The login method will first try using the acquireTokenSilentAsync function to login using the details stored in the keychain.

```javascript
import {MSAdalLogin, MSAdalLogout} from "react-native-ms-adal";

const authority = "https://login.windows.net/common";
const resourceUri = "https://graph.windows.net";

const clientId = <your-client-id>;
const redirectUri = <your-redirect-uri>;

const msAdalPromise = MSAdalLogin(authority, clientId, redirectUri, resourceUri)
  .then(authDetails => {
    // Get the data from the server, using the Authorisation Header
    fetch("<your-url>", {
      headers: {
        "Cache-Control": "no-cache",
        Authorization: "Bearer " + authDetails.accessToken
      }
    })
      .then(response => {
        if (response.status === 200) {
          return response.json();
        } else {
          throw new Error(
            "Server returned status: " +
              response.status +
              ": " +
              response.statusText
          );
        }
      })
      .then(json => {
        // etc
      });
  })
  .catch(err => {
    if (err.code === "403") {
      // User has cancelled
      // We need to make sure the login button is displayed
    }
    console.log("Failed to authenticate: " + err);
  });

```


See the [Microsoft Azure Active Directory Authentication Library (ADAL) for iOS and OSX](https://github.com/AzureAD/azure-activedirectory-library-for-objc) for full instructions on how to configure the keychain etc in xcode.

See the [Microsoft Azure Active Directory Authentication Library (ADAL) for Android](https://github.com/AzureAD/azure-activedirectory-library-for-android) for full instructions on how to configure necessary permissions in Android.
