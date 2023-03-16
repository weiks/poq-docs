# Integrating with Unity mobile SDK

If you're developing a mobile Unity game we provide a [Unity SDK](https://github.com/weiks/quarters-unity-sdk) that makes integrating Quarters into your game simple and intuitive. 
Below is an 3 step explanation of how to setup that integration.

## 1) Creating the PoQ App
To use PoQ's APIs or SDK, you first need to have [a PoQ account](https://www.poq.gg/login) and ask for developer permissions on your [user profile](https://www.poq.gg/profile).

![image](https://user-images.githubusercontent.com/3865131/225700156-7ecdec01-73ba-44b0-b39e-4bc46c34269d.png)

Once you asked dev permissions you can contact us on [`PoQ Game Devs` discord server](https://discord.gg/yQxYgRx3n8) to speed up your grants or just wait.    

Using that account you can create your PoQ app.

  1. Go to https://poq.com/dev
  2. Select `Create` button.
  3. Fill in the application form, don't worry if you're not sure about any of the fields, you can edit them again later.

## 2) Linking your PoQ and Unity apps
Once you have step one completed, you need to create a 2-way linking between it and the Unity application itself.

During either of the two linking processes below, if it's your first linking, you will be asked to provide a unique identifier.
That identifier will be used to provide you with a unique PoQ subdomain like this: `https://example.games.poq.gg`

### Linking with iOS

1. Login on your Apple developer account in https://developer.apple.com/
2. In Identifiers -> App IDs, create or edit the app you wish to integrate with PoQ.
3. Check the Associated Domains checkbox
4. Save the changes

The app's page will now mention a **prefix** and an **ID** at the top, keep those handy.

5. Go to https://poq.gg/dev (login if you're not logged in)
6. Locate your app and click on `Manage` button next to it
7. Click the `iOS Config` button at the bottom of the page  
9. Fill in the Team ID field as with the prefix value from above and fill in the bundle ID field with ID field from above
![image](https://user-images.githubusercontent.com/3865131/225700273-d5119153-b221-414b-8854-44152aa82e57.png)
10. Click `Save changes` button and you should be done!

### Linking with Android

1. Find out your Android package name by opening your Unity Project and then clicking Edit -> Project Settings -> Player. In the Android options you will find the Package Name, keep it handy. If it's not set up, set one up, you can follow [Google's guidelines for how to choose a package name](https://developer.android.com/studio/build/configure-app-module#set_the_application_id)
2. Next you need to make sure you have a keystore, you can read more about what they are and how to generate them [in here](https://developer.android.com/studio/publish/app-signing). After you have a keystore you can run this command:
    ```
    keytool -list -v -keystore mystorekeystore.keystore
    ```
    _and copy the long value with lots of colons that appears next to SHA256 that looks like this_ `14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5`
     <br><br>

3. Go to https://poq.gg/dev (login if you're not logged in)
6. Locate your app and click on `Manage` button next to it
7. Click the `Android Config` button at the bottom of the page  
8. Fill in the package name with the package name you got from step 1, fill in the certificate fingerprint with the value you got from step 2
![image](https://user-images.githubusercontent.com/3865131/225700399-74869c4d-dea9-410a-80a5-174eae9f6beb.png)
9. Click `Save changes` button and you should be done!

⚠️ Important notes: In your game description you can see your 'client_id', 'client_secret' and 'App Uri' that might need for your development.
