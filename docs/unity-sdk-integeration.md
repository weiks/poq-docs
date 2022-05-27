# Integrating with Unity mobile SDK

If you're developing a mobile Unity game we provide a [Unity SDK](https://github.com/weiks/quarters-unity-sdk) that makes integrating Quarters into your game simple and intuitive. Below is an explanation of how to setup that integration

## Setup
To use PoQ's APIs or SDK you first need to have [a PoQ account](https://www.poq.gg/?action=signup) and using that account you can create your PoQ app.

1. Go to https://apps.pocketfulofquarters.com
2. Sign in with the same information you used to register on `poq.gg`
3. Fill in the application form, don't worry if you're not sure about any of the fields, you can edit them again later.

Once you have an application setup you need to create 2-way linking between it and the Unity application

During either of the two linking processes below, if it's your first linking, you will be asked to provide a unique identifier that will be used to provide you with a unique poq subdomain like this: `https://example.games.poq.gg`

### Linking with iOS

1. Login on your Apple developer account in https://developer.apple.com/
2. In Identifiers -> App IDs, create or edit the app you wish to integrate with PoQ.
3. Check the Associated Domains checkbox
4. Save the changes

The app's page will now mention a prefix and an ID at the top, keep those handy.

5. Go to https://poq.gg/apps (login if you're not logged in)
6. Locate your app and click on the `iOS` button next to it
7. Choose a unique identifier if you haven't already
8. Fill in the Team ID field as with the prefix value from above and fill in the bundle ID field with ID field from above
9. Click submit and you should be done!

### Linking with Android

1. Find out your Android package name by opening your Unity Project and then clicking Edit -> Project Settings -> Player. In the Android options you will find the Package Name, keep it handy. If it's not set up, set one up, you can follow [Google's guidelines for how to choose a package name](https://developer.android.com/studio/build/configure-app-module#set_the_application_id)
2. Next you need to make sure you have a keystore, you can read more about what they are and how to generate them [in here](https://developer.android.com/studio/publish/app-signing). After you have a keystore you can run this command:
    ```
    keytool -list -v -keystore mystorekeystore.keystore
    ```
    and copy the long value with lots of colons that appears next to SHA256 that looks like this `14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5`

3. Go to https://poq.gg/apps (login if you're not logged in)
4. Choose a unique identifier if you haven't already
5. Fill in the package name with the package name you got from step 1, fill in the certificate fingerprint with the value you got from step 2
6. Click submit and you should be done!

### After linking

You can then take the unique URL you were assigned and use it as your app's URL in https://apps.pocketfulofquarters.com

