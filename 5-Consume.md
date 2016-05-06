# Lab 5: Consuming the IoT Hub data
You have seen how to use the Device Explorer to peek the data being sent to the Azure IoT Hub. However, the Azure IoT suite offers many different ways to generate meaningful information from the data gathered by the devices. In the following sections you will explore two of them: You will see how the Azure Services Bus Messaging system can be used in a Website (part of the ConnectTheDots project), and how to use Azure Stream Analytics in combination with Microsoft Power BI to consume the data and to generate meaningful reports.

1. (Optional) [Using Power BI](5-PowerBI.md)
2. [From a Website](#Task52)

<a name="Task52" />
## Lab 5b: Consuming the IoT Hub data from a Website

	![Lab 5 Overview](Images/5b-HOL.png?raw=true)

	_Overview Lab 5_


1. Before starting you need to create Consumer Groups to avoid colliding with the Stream Analytics job, in the same way you did in the [Using Power BI section](Task3220). The website needs two different consumer groups:
  - _website_
  - _local_ (used when debugging)
  
	![Adding website consumer groups](Images/adding-website-consumer-groups.png?raw=true)
  
2. Also take note of the **Event Hub-compatible name** and **Event Hub-compatible endpoint** values in the _Messaging_ blade

3. Browse to the **Assets/WebSite** folder and open the Web Site project (_ConnectTheDotsWebSite.sln_) in Visual Studio.

4. Edit the _Web.config_ file and add the corresponding values for the following keys:
	- **Microsoft.ServiceBus.EventHubDevices**: Event hub-compatible name.
	- **Microsoft.ServiceBus.ConnectionStringDevices**: Event hub-compatible connection string which is composed by the **Event hub-compatible endpoint** and the **_iothubowner_ Shared access policy Primary Key**.
	![IoT Hub shared access policy primary key](Images/iot-hub-shared-access-policy-primary-key.png?raw=true)
	
	- **Microsoft.Storage.ConnectionString**: Storage account endpoint, in this case use the **storage account name** and **storage account primary key** to complete the endpoint.

5. Deploy the website to an Azure Web Site. To do this, perform the following steps.
	1. In Visual Studio, right-click on the project name and select **Publish**.
	2. Select **Microsoft Azure Web Apps**.

		![Selecting Publish Target](Images/selecting-publish-target.png?raw=true)

		_Selecting Publish target_

	3. Click **New** and use the following configuration.

		- **Web App name**: Pick something unique.
		- **App Service plan**: Select an App Service plan in the same region used for _Stream Analytics_ or create a new one using that region.
		- **Region**: Pick same region as you used for _Stream Analytics_.
		- **Database server**: No database.

	4. Click **Create**. After some time the website will be created in Azure.

		![Creating a New Web App](Images/creating-a-new-web-app.png?raw=true)

		_Creating a new Web App on Microsoft Azure_

	3. Click **Publish**.

		> **Note:** You might need to install **WebDeploy** extension if you are having an error stating that the Web deployment task failed. You can find WebDeploy [here](http://www.iis.net/downloads/microsoft/web-deploy).


6. After you deployed the site, it is required that you enable **Web sockets**. To do this, perform the following steps:
	1. Browse to https://portal.azure.com and select your _Azure Web App.
	2. Click **All settings**.
	3. Click **Applicattion settings**
	4. Then set **Web sockets** to **On** and click **Save**.

		![Enabling Web Sockets](Images/enabling-web-sockets.png?raw=true)

		_Enabling Web Sockets in your website_

7. Browse to your recently deployed Web Application. You will see something like in the following screenshot. There will be 2 real-time graphics representing the values read from the temperature and light sensors. Take into account that the Universal app must be running and sending information to the IoT Hub in order to see the graphics.

	![Web Site Consuming the IoT Hub Data](Images/web-site-consuming-the-event-hub-data.png?raw=true)

	_Web Site Consuming the IoT Hub data_

	> **Note:** At the bottom of the page you should see “**Connected**.”. If you see “**ERROR undefined**” you likely didn’t enable **WebSockets** for the Azure Web Site.
