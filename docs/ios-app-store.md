# Uploading to the IOS App Store

1. In `ProjectSettings/ProjectSettings.asset` make sure under `Application Identifier:` you have the line `iPhone: com.pocketfulofquarters.youruniqueidentifier`

##Unity Games
###Note: You will not be able to run games in the simulator. You will need a physical device plugged in to your computer
1. Open the Game in the Unity Editor
2. Go to File --> Build Settings
3. Select IOS and "Switch Platform
    a. If you don't have IOS installed "Install with Unity Hub"
4. File --> Build and Run
You may encounter an error at first due to build settings being improperly set

##Build Settings in Xcode
1. Navigate to Signing in Capabilities by clicking "Unity-iPhone" in the left sidebar--> Under Targets select "Unity-iPhone" --> Signing and Capabilites
2. Check "Automatically Manage Signing"
3. Make sure that Team is set to "Pocketful Of Quarters, Inc"
4. Check that Bundle Id is set to `com.pocketfulofquarters.youruniqueidentifier`
    - if it is incorrect go back and check `ProjectSettings/ProjectSettings.asset`

    //ss
5. Click on + Capability and add "Associated Domains"
    put in `applinks:youruniqueidentifier.games.poq.gg`
 //ss
6. You should now be able to run the app

##Creating Build For App Store
1. Product --> Archive
2. Go to [App Store Connect](https://appstoreconnect.apple.com/apps) and your app should be there. It may take ~10-20 minutes for the app to appear.