![Midea Smart Technology Co., LTD.](http://msmart.midea.com/images/brand.png) 
# MideaSDK 3.0 Quick User Manual
> Copyright 2017 Midea Smart Technology Co.,Ltd.


# 1. Abstract
MideaSDK is a software develop kit maintained by MSmart. You can develop your own APP, Smart Hardware or Smart TV based on this SDK to control the smart appliances produced by Midea.

# 2. main features
SDK provide these features：   
1.  registeration and login;     
2.  configure smart appliance and bind with account;
3.  scan smart appliance in LAN;
4.  send command to appliance through LAN or WAN    
    
# 3. Before integration

You need to prepare the following items:
1. A wifi router support 2.4G frequency
2. A smart appliance produced by Midea
3. Package of MideaSDK V3.x 
4. Get AppID,AppKey and AppSrc which assigned by Midea
5. A cell phone with Android 4.0 or above  

> Note: The smart appliance(with wifi module) can only support 2.4GHz wifi signal.  





# 4. Configure SDK
> There are three folders in the SDK package: **jar**, **javadoc**,**jni**，which contain java lib.，API Doc. and dynamic so lib. files. 

Please follow these steps:
1. copy the jar files to the folder "libs" of your project.
2. copy the so files to the related jni folder.
3. call the SDK initial API when the appliance control module of APP started.
> Note: the project menu is vary from project to project, please do the configuration based on the actual situation.

Permission settings in Android：

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>  
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.FLASHLIGHT" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" />

> **Note**：
> Most of the cell phone is running android system with version 5.0 or 6.0. Google add dynamic permission from android 6.0. SDK need these dynamic permissions:

    Manifest.permission.ACCESS_FINE_LOCATION
    Manifest.permission.ACCESS_COARSE_LOCATION

>- We suggest that you can check these two permissions when your APP starting.
>- The defaults permissions are usually closed in the new sell phone, you  can check here: *setting*---*APP*---*your APP*---*permissions*.
>- You need to open GPS on android 6.0 or above for the network limitation of Android system.


#5. How to configure and control a new smart appliance

##5.1 SDK initialization


You need to get there authnticated parameters "AppKey，AppID and AppSrc" assigned by Midea.

    MSmartSDK.getInstance().initSDKWithAppID(Context, appID, appKey, appSrc);


> You can't call any API until you initialize the SDK. We suggest that you can do the initialization in the onCreate() module of your application.

##5.2 Register and login

You need to register a account with email. You will receive an activation email send from Midea once register succeed. You can activate your account following the guide in the email. You can call this API:

    void registerWithEmail(String email,
                           String password,
                           String nickName,
                           MSmartCallback callback)

You can login to Midea Smart system once you get a midea account by this API:

    void loginWithAccount(String account,
                          String password,
                          android.os.Bundle extraData,
                          MSmartCallback callback)


> Note: We can set that the registered account is activated by default.(no need to activate from email)。

##5.3 Configure a new appliance
Configure a new appliance means to make it connect to WiFi network and bind with your account.   
There are two mode can be used to configure the Midea Smart Appliances: AP mode and Midea Smart Config(MSC) mode.    

>You can find the appliance configuration related API in *MSmartUserDeviceManager*.
<table>
        <tr>
            <th>API</th>
            <th>Description</th>
        </tr>
        <tr>
            <th>startConfigureDevice</th>
            <th>start</th>
        </tr>
        <tr>
            <th>resumeConfigureDevice</th>
            <th>resume</th>
        </tr>
        <tr>
            <th>stopConfigureDevice</th>
            <th>stop</th>
        </tr>
</table>


The state machine of the configuration is shown below:

![state machine](config_status_trans.png)  

There will be a error callback no matter what kind of error accured. If there is boolean type data *manualOperation* and the value is "ture", The state machine will running into waiting mode. You can call the API "resumeConfigureDevice" to resume the state machine once finished the required operation.
> Note：we suggest that you can call "stopConfigureDevice" before starting a new configuration to avoid unkown issues.  

##5.4 Scan appliances in LAN

You can find the supported smart appliances in the LAN through SDK.

<table>
        <tr>
            <th>API</th>
            <th>Description</th>
        </tr>
        <tr>
            <th>startScanLanDevice</th>
            <th>start scanning</th>
        </tr>
        <tr>
            <th>registerDeviceScanListener</th>
            <th>register listener</th>
        </tr>
        <tr>
            <th>removeDeviceScanListener</th>
            <th>remove listener</th>
        </tr>
</table>

Both "startScanLanDevice" and "registerDeviceScanListener" can find the appliances in LAN. They can be used in different scene.  
<table>
        <tr>
            <th>API</th>
            <th>Description</th>
        </tr>
        <tr>
            <th>startScanLanDevice</th>
            <th>This API can start scanning immediately within a specific time slot.</th>
        </tr>
        <tr>
            <th>registerDeviceScanListener</th>
            <th>This API can be used to listen the SDK. There will be a callback once find a new appliance.</th>
        </tr>
</table>
 
    void startScanLanDevice(int time,MSmartDataCallback<List<android.os.Bundle>> callback)

You can use the following code to find an appliance quickly.

    //Start a scanning
    mDeviceManager.startScanLanDevice(0,null);
    mDeviceManager.registerDeviceScanListener(listener);


##5.5 Control a configured appliance
   
You can use this API to send command to an appliance.

    public void sendDeviceData(String deviceID, byte[] data,MSmartDataCallback<byte[]> callback);

The timeout setting of this data sending API is 10 seconds. 
> Note: You need to package the query and control commands to binary string following the protocol of different appliance type.
You also need to parse the binary string in the API Callback using the same protocol.
The specific protocol you can obtain from your BD.



##5.6 The principles of the API
Most of SDK APIs are async API. That means you need to define a "callback" function while using the APIs. The callback data is in the bundle data.
There are three kinds of callback:
<table>
        <tr>
            <th>Callback</th>
            <th>Description</th>
        </tr>
        <tr>
            <th>MSmartCallback</th>
            <th>Only retuen Succeed to Failed. No callback data.</th>
        </tr>
        <tr>
            <th>MSmartDataCallback</th>
            <th>Callback data is in the bundle if succeed.</th>
        </tr>
        <tr>
            <th>MSmartStepDataCallback</th>
            <th>Inherited from MSmartDataCallback, contains addtional data.</th>
        </tr>
</table>

> Most of the calback type are "MSmartDataCallback<Bundle>", "MSmartStepDataCallback" is mainly used in appliance configuration.

Aall the SDK APIs are thread-safe. All the callbacks are running in the main thread.  
For example:

    MSmartUserManager mUserManager = (MSmartFamilyDeviceManager) 
                      MSmartSDK.getInstance().getSDKManager(MSmartSDK.DEVICE_MANAGER_NAME);  
    mUserManager.loginWithAccount(userAccount, password, extraData, new MSmartCallback() {
                    @Override
                    public void onComplete() {
                        //在主线程
                    }
                    @Override
                    public void onError(MSmartErrorMessage errMsg) {
                        //在主线程
                    }
                }
        );




#6. Other useful APIs

## 6.1 Enable the log of SDK:
You can enable log module of SDK to help you debugging the aaplication.

    void enableLog(boolean isEnabled)
> Note: You must close the log module when you finish the debugging and publish to Google Play.

##6.2 Data synchronization：
You can sync the data between SDK and M-Cloud by this API:

    void syncCloudData()

> Note：SDK will buffer the user and appliance list information locally, maybe the data will be not synchronized. You can call this API if more than one user can control an appliance.
