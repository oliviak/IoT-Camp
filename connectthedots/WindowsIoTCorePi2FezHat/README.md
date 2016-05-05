Windows 10 IoT Core Hands-on Lab
========================================
ConnectTheDots will help you get tiny devices connected to Microsoft Azure, and to implement great IoT solutions taking advantage of Microsoft Azure advanced analytic services such as Azure Stream Analytics and Azure Machine Learning.

> This lab is stand-alone, but is used at Microsoft to accompany a presentation about Azure, Windows 10 IoT Core, and our IoT services. If you wish to follow this on your own, you are encouraged to do so. If not, consider attending a Microsoft-led IoT lab in your area.



In this lab you will use a [Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) device with [Windows 10 Iot Core](http://ms-iot.github.io/content/en-US/Downloads.htm) and a [FEZ HAT](https://www.ghielectronics.com/catalog/product/500) sensor hat. Using a Windows 10 Universal Application, the sensors get the raw data and format it into a JSON string. That string is then shuttled off to the [Azure IoT Hub](https://azure.microsoft.com/en-us/services/iot-hub/), where it gathers the data and is then displayed in a chart using Power BI.
The JSON string is sent to the IoT Hub in one of two ways: packaged into an AMQP message or in a REST packet. If you use AMQP, which is the recommended approach, you will need to have the AMQP port open on your firewall/router.

> **Note:** Although AMQP is the recommended approach, at the time this lab was written that protocol was not supported by the [Azure IoT Core SDK](https://github.com/Azure/azure-iot-sdks) for UWP (Universal Windows Platform) applications. However, it is expected to be implemented in a short time, since it's currently under development.  


This lab includes the following tasks:

1. [Setup: Software & Device](1-Setup.md)
2. [Setup Azure](2-SetupAzure.md)
	1. Azure account and services
	2. Device Registration
3. [Create a Universal App: Read sensor data from FEZ HAT](3-DataRead.md)
4. [Send telemetry data to Azure IoT Hub](4-DataSend.md)
5. [Consume the IoT Hub data](5-Consume.md)
	1. (Optional) Using Power BI
	2. From a Website
6. [Send commands to your device](6-CommandControl.md)
7. [Summary](#Summary)



<a name="Summary" />
## Summary
In this lab, you have learned how to create a Universal app that reads from the sensors of a FEZ hat connected to a Raspberry Pi 2 running Windows 10 IoT Core, and upload those readings to an Azure IoT Hub. You also learned how to read and consume the information in the IoT Hub using Power BI to get near real-time analysis of the information gathered from the FEZ hat sensors and to create simple reports and how to consume it using a website. You also saw how to use the IoT Hubs Cloud-To-Device messages feature to send simple commands to your devices.
