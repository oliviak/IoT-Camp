# Lab 2: Setup Azure

You will need a Microsoft Azure subscription ([free trial subscription] (http://azure.microsoft.com/en-us/pricing/free-trial/) is sufficient)

1. [Create an IoT Hub](#Task21)
2. [Create a Stream Analytics Job](#Task22)
3. [Register your device](#Task23)

<a name="Task21" />
## 1. Creating an IoT Hub

1. Enter the Azure portal, by browsing to http://portal.azure.com
2. Create a new IoT Hub. To do this, click **New** in the jumpbar, then click **Internet of Things**, then click **Azure IoT Hub**.

	![Creating a new IoT Hub](Images/creating-a-new-iot-hub.png?raw=true "Createing a new IoT Hub")

	_Creating a new IoT Hub_

3. Configure the **IoT hub** with the desired information:
 - Enter a **Name** for the hub e.g. _iot-sample_,
 - Select a **Pricing and scale tier** (_F1 Free_ tier is enough),
 - Create a new resource group, or select and existing one. For more information, see [Using resource groups to manage your Azure resources](https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/).
 - Select the **Region** such as _East US_ where the service will be located.

	![new iot hub settings](Images/new-iot-hub-settings.png?raw=true "New IoT Hub settings")

	_New IoT Hub Settings_

4. It can take a few minutes for the IoT hub to be created. Once it is ready, open the blade of the new IoT hub, take note of the URI and select the key icon at the top to access to the shared access policy settings:

	![IoT hub shared access policies](Images/iot-hub-shared-access-policies.png?raw=true)

5. Select the Shared access policy called **iothubowner**, and take note of the **Primary key** and **connection string** in the right blade.  You should copy these into a text file for future use. 

	![Get IoT Hub owner connection string](Images/get-iot-hub-owner-connection-string.png?raw=true)


<a name="Task22" />
## 2. Creating a Stream Analitycs Job

To create a Stream Analytics Job, perform the following steps.

1. Currently the new Azure Portal doesn't support all of the features of Stream Analytics required for this lab.  For that reason, the Stream Analaytics configuration needs to be done in the **classic portal**.   To open the classic portal, in your browser navigate to https://manage.windowsazure.com and login in with your Azure Subscription's credentials. 

1. In the Azure Management Portal, click **NEW**, then click **DATA SERVICES**, then **STREAM ANALYTICS**, and finally **QUICK CREATE**.

2. Enter the **Job Name**, select a **Region**, such as _East US_ (if it is an option, create the Stream Analytics Job in the same region as your IoT Hub) and the enter a **NEW STORAGE ACCOUNT NAME** if this is the first storage account in the region used for Stream Analitycs, if not you have to select the one already used for that region.

3. Click **CREATE A STREAM ANALITYCS JOB**.

	![Creating a Stream Analytics Job](Images/createStreamAnalytics.png?raw=true)

	_Creating a Stream Analytics Job_

4. After the _Stream Analytics_ job is created, in the left pane click **STORAGE**, then click the account you used in the previous step, and click **MANAGE ACCESS KEYS**. As with the IoT Hub details, copy the **STORAGE ACCOUNT NAME** and the **PRIMARY ACCESS KEY** into a text file as you will use those values later.

	![manage access keys](Images/manage-access-keys.png?raw=true)

	_Managing Access Keys_

<a name="Task23" />
## 3. Registering your device
You must register your device in order to be able to send and receive information from the Azure IoT Hub. This is done by registering a [Device Identity](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/#device-identity-registry) in the IoT Hub.

1. Open the Device Explorer app (downloard [here](https://github.com/Azure/azure-iot-sdks/releases/download/2016-04-22/SetupDeviceExplorer.msi)) (C:\Program Files (x86)\Microsoft\DeviceExplorer\DeviceExplorer.exe) and fill the **IoT Hub Connection String** field with the connection string of the IoT Hub you created in previous steps and click on **Update**.

	![Configure Device Explorer](Images/configure-device-explorer.png?raw=true)

2. Go to the **Management** tab and click on the **Create** button. The Create Device popup will be displayed. Fill the **Device ID** field with a new Id for your device (_myFirstDevice_ for example) and click on Create:

	![Creating a Device Identity](Images/creating-a-device-identity.png?raw=true)

3. Once the device identity is created, it will be displayed in the grid. Right click on the identity you just created, select **Copy connection string for selected device** and take note of the value copied to your clipboard, since it will be required to connect your device with the IoT Hub.

	![Copying Device connection information](Images/copying-device-connection-information.png?raw=true)

	> **Note:** The device identities registration can be automated using the Azure IoT Hubs SDK. An example of how to do that can be found [here](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-csharp-csharp-getstarted/#create-a-device-identity).