# Lab 6: Sending commands to your devices
Azure IoT Hub is a service that enables reliable and secure bi-directional communications between millions of IoT devices and an application back end. In this section you will see how to send cloud-to-device messages to your device to command it to change the color of one of the FEZ HAT leds, using the Device Explorer app as the back end.

	![Lab 6 Overview](Images/6-HOL.png?raw=true)

	_Overview Lab 6_


1. Open the Universal app you created before and add the following method to the **ConnectTheDotsHelper.cs** file:

	````C#
	public async Task<string> ReceiveMessage()
	{
		if (this.HubConnectionInitialized)
		{
			try
			{
				var receivedMessage = await this.deviceClient.ReceiveAsync();

				if (receivedMessage != null)
				{
					var messageData = Encoding.ASCII.GetString(receivedMessage.GetBytes());
					this.deviceClient.CompleteAsync(receivedMessage);
					return messageData;
				}
				else
				{
					return string.Empty;
				}
			}
			catch (Exception e)
			{
				Debug.WriteLine("Exception when receiving message:" + e.Message);
				return string.Empty;
			}
		}
		else
		{
			return string.Empty;
		}
	}
	````

	The _ReceiveAsync_ method returns the received message at the time that it is received by the device. The call to _CompleteAsync()_ notifies IoT Hub that the message has been successfully processed and that it can be safely removed from the device queue. If something happened that prevented the device app from completing the processing of the message, IoT Hub will deliver it again.
	
2. Now you will add the logic to process the messages received. Open the **MainPage.xaml.cs** file and add a new timer to the _MainPage_ class:

	````C#
	DispatcherTimer commandsTimer;
	````

3. Add the following method, which will be on charge of processing the commands:

	````C#
	private async void CommandsTimer_Tick(object sender, object e)
	{
		string message = await ctdHelper.ReceiveMessage();

		if (message != string.Empty)
		{
			System.Diagnostics.Debug.WriteLine("Command Received: {0}", message);
			switch (message.ToUpperInvariant())
			{
				case "RED":
					hat.D2.Color = new FEZHAT.Color(255, 0, 0);
					break;
				case "GREEN":
					hat.D2.Color = new FEZHAT.Color(0, 255, 0);
					break;
				case "BLUE":
					hat.D2.Color = new FEZHAT.Color(0, 0, 255);
					break;
				case "OFF":
					hat.D2.TurnOff();
					break;
				default:
					System.Diagnostics.Debug.WriteLine("Unrecognized command: {0}", message);
					break;
			}
		}
	}
	````

	It reads the message received, and according to the text of the command, it set the value of the _hat.D2.Color_ attribute to change the color of the FEZ HAT's LED D2. When the "OFF" command is received the _TurnOff()_ method is called, which turns the LED off.
	
4. Lastly, add the following piece of code to the _SetupHatAsync_ method in order to initialize the timer used to poll for messages. 

	````C#
	this.commandsTimer = new DispatcherTimer();
	this.commandsTimer.Interval = TimeSpan.FromSeconds(60);
	this.commandsTimer.Tick += this.CommandsTimer_Tick;
	this.commandsTimer.Start();
	````

	> **Note:** The recommended interval for HTTP/1 message polling is 25 minutes. For debugging and demostration purposes a 1 minute polling interval is fine (you can use an even smaller interval for testing), but bear it in mind for production development. Check this [article](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/) for guidance. When AMQP becomes available for the IoT Hub SDK using UWP apps a different approach can be taken for message processing, since AMQP supports server push when receiving cloud-to-device messages, and it enables immediate pushes of messages from IoT Hub to the device. The following [article](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-csharp-csharp-c2d/) explains how to handle cloud-to-device messages using AMQP.
	
5. Deploy the app to the device and open the Device Explorer app.

6. Once it's loaded (and configured to point to your IoT hub), go to the **Messages To Device** tab, check the **Monitor Feedback Endpoint** option and write your command in the **Message** field. Click on **Send**

	![Sending cloud-to-device message](Images/sending-cloud-to-device-message.png?raw=true)

7. After a few seconds the message will be processed by the device and the LED will turn on in the color you selected. The feedback will also be reflected in the Device Explorer screen after a few seconds.

	![cloud-to-device message received](Images/cloud-to-device-message-received.png?raw=true)

