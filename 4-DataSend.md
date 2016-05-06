# Lab 4: Send telemetry data to the Azure IoT Hub

Now that you know how to read the FEZ HAT sensors data, you will send that information to an Azure IoT Hub.

![Lab 4 Overview](Images/4-HOL.png?raw=true)

_Overview of Lab 4_


To do that, you will use an existing project located in the **Code\WindowsIoTCorePi2FezHat\Begin** folder. This project is based on the [ConnectTheDots Raspberry Pi with Windows 10 IoT sample project](https://github.com/Azure/connectthedots/tree/master/Devices/DirectlyConnectedDevices/WindowsIoTCorePi2) but using the [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) to connect with Azure, instead of using an Event Hub.

1. Open the solution located in the **Code\WindowsIoTCorePi2FezHat\Begin** folder.

2. Before running the application, you must set the **Device connection** information. Go to the _MainPage_ method of the _MainPage.xaml.cs_ file and replace **IOT_CONNECTION_STRING** with your device connection string, obtained in previous steps using the Device Explorer app:

	````C#
	ctdHelper = new ConnectTheDotsHelper(iotDeviceConnectionString: "IOT_CONNECTION_STRING",
	    organization: "YOUR_ORGANIZATION_OR_SELF",
	    location: "YOUR_LOCATION",
	    sensorList: sensors);
	````

	![Copying Device connection information](Images/copying-device-connection-information.png?raw=true)

	> **Note:** An **Organization** and **Location** may also be provided. Those values will be part of the telemetry message payload, and could be used to get a better classification of the data received.

3. 	Before the app can be deployed you need to change the solution target platform, since the Raspberry Pi is based on the ARM architecture. To do that select **ARM** in the **Solution Platform** dropdown:

	![Set Solution Platform](Images/set-solution-platform.png?raw=true)

	_Setting the Solution Platform_

	As you can see in the sample code, the app uses a button in the UI to _simulate_ a real sensor. Every time the user presses the button the value of the sensor is incremented and that info is then sent to Azure.

	````C#
	private void Button_Click(object sender, RoutedEventArgs e)
	{
	    ConnectTheDotsSensor sensor = ctdHelper.sensors.Find(item => item.measurename == "Temperature");
	    sensor.value = counter++;
	    ctdHelper.SendSensorData(sensor);
	}
	````

4. For those devices lacking a monitor or display, the button will be replaced by a **Timer** so the same function can be performed without needing to click any button. If this is your case, perform the following steps.

	1. Replace the **Button_Click** method for this one:

		````C#
		private void Timer_Tick(object sender, object e)
		{
		    ConnectTheDotsSensor sensor = ctdHelper.sensors.Find(item => item.measurename == "Temperature");
		    sensor.value = counter++;
		    ctdHelper.SendSensorData(sensor);
		}
		````

	2. Then, replace the call to the **Button_Click** method in the **MainPage** method for the following piece of code:

		````C#
		//Button_Click(null, null);
		var timer = new DispatcherTimer();
		timer.Interval = TimeSpan.FromMilliseconds(500);
		timer.Tick += Timer_Tick;
		timer.Start();
		````

		Which will make the Timer ticks twice a second.

	3. Lastly, remove the button from the UI:

		<!-- mark:4 -->
		````XAML
		<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
			<StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
				<TextBox x:Name="HelloMessage" Text="ConnectTheDots on IoT" Margin="10" IsReadOnly="True"/>
				<!--<Button x:Name="ClickMe" Content="Click Me!"  Margin="10" HorizontalAlignment="Center" Click="Button_Click"/>-->
			</StackPanel>
		</Grid>
		````


5. Before adding real sensor information you can run this code to see how the device connects to your **Azure IoT Hub** and sends information. Run the application.

	![Debug Console output](Images/debug-console-output.png?raw=true)

	_Debugging in the Output Window_

6. After the app has been successfully deployed, it can start sending messages to the IoT Hub. If the device is connected to a display and a mouse, click the **Click Me** button several times.

	The information being sent can be monitored using the Device Explorer application. Run the application and go to the **Data** tab and select the name of the device you want to monitor (_myFirstDevice_ in your case), then click on **Monitor**

	![Monitoring messages sent](Images/monitoring-messages-sent.png?raw=true)

	> **Note:** If the Device Explorer hub connection is not configured yet, you can follow the instructions explained in the [Registering your device](#Task14) section

7. If you followed the instructions for devices without a screen attached, you will need to delete the timer created in that flow before you continue. A new timer will be created in the next steps replacing the previous one. Remove the **Timer_Tick** method you created before and delete the following lines from the **MainPage** constructor

	````C#
	var timer = new DispatcherTimer();
	timer.Interval = TimeSpan.FromMilliseconds(500);
	timer.Tick += Timer_Tick;
	timer.Start();
	````

8. Now that the device is connected to the Azure IoT Hub, add some real sensor information. First, you need to add a reference the FEZ HAT drivers. To do so, instead of manually adding the projects included in the GHI Developer's Guide, you will install the [NuGet package](https://www.nuget.org/packages/GHIElectronics.UWP.Shields.FEZHAT/ "FEZ HAT NuGet Package") that they provide. To do this, open the **Package Manager Console** (Tools/NuGet Package Manager/Package Manager Console) and execute the following command:

	````PowerShell
	PM> Install-Package GHIElectronics.UWP.Shields.FEZHAT
	````

	![Intalling GHI Electronics NuGet package](Images/intalling-ghi-electronics-nuget-package.png?raw=true)

	_Installing the FEZ hat Nuget package_

9. Add a reference to the FEZ HAT library namespace in the _MainPage.xaml.cs_ file:

	````C#
	using GHIElectronics.UWP.Shields;
	````

10. Declare the variables that will hold the reference to the following objects:
 - **hat**: Of the type **Shields.FEZHAT**, will contain the hat driver object that you will use to communicate with the FEZ hat through the Raspberry Pi hardware.
  - **telemetryTimer**: of the type **DispatchTimer**, that will be used to poll the hat sensors at regular basis. For every _tick_ of the timer the value of the sensors will be get and sent to Azure.

	````C#
	FEZHAT hat;
	DispatcherTimer telemetryTimer;
	````

11. You will add the following method to initialize the objects used to handle the communication with the hat. The **TelemetryTimer_Tick** method will be defined next, and will be executed every 500 ms according to the value hardcoded in the **Interval** property.

	````C#
	private async Task SetupHatAsync()
	{
	    this.hat = await FEZHAT.CreateAsync();

	    this.telemetryTimer = new DispatcherTimer();

	    this.telemetryTimer.Interval = TimeSpan.FromMilliseconds(500);
	    this.telemetryTimer.Tick += this.TelemetryTimer_Tick;

	    this.telemetryTimer.Start();
	}
	````

12. The following method will be executed every time the timer ticks, and will poll the value of the hat's temperature sensor, send it to the Azure IoT Hub and show the value obtained.

	````C#
	private void TelemetryTimer_Tick(object sender, object e)
	{
	    // Temperature Sensor
	    var tSensor = ctdHelper.sensors.Find(item => item.measurename == "Temperature");
	    tSensor.value = this.hat.GetTemperature();
	    this.ctdHelper.SendSensorData(tSensor);

	    this.HelloMessage.Text = "Temperature: " + tSensor.value.ToString("N2");

	    System.Diagnostics.Debug.WriteLine("Temperature: {0} °C", tSensor.value);
	}
	````

	The first statement gets the _ConnectTheDots_ sensor from the sensor collection already in place in the project (the temperature sensor was already included in the sample solution). Next, the temperature is polled out from the hat's temperature sensor using the driver object you initialized in the previous step. Then, the value obtained is sent to Azure using the _ConnectTheDots_'s helper object **ctdHelper** which is included in the sample solution.

	The last two lines are used to show the current value of the temperature sensor to the screen and the debug console respectively.

	> **Note:** To show the value of the sensor in the screen the app is using the Welcome message textbox. In the following steps the UI will be improved.

13. Before running the application you need to add the call to the **SetupHatAsync** method. Replace the **Page_Loaded** method with this block of code:

	````C#
	private async void Page_Loaded(object sender, RoutedEventArgs e)
	{
		// Initialize FEZ HAT shield
		await SetupHatAsync();
	}
	````

	> Note that the **async** modifier was added to the event handler to properly handle the asynchronous call to the FEZ HAT initialization method.

14. Now you are ready to run the application. Connect the Raspberry Pi with the FEZ HAT and run the application. After the app is deployed you will start to see in the output console (also in the screen) the values polled from the sensor. The information sent to Azure is also shown in the console.

	![Console output](Images/console-output.png?raw=true)

	_Output Window_

	You can also check that the messages were successfully received by monitoring them using the Device Explorer

	![Telemetry messages received](Images/telemetry-messages-received.png?raw=true)

15. Now you will add the information from another sensor. To incorporate the data from the Light sensor you will need to add a new _ConnectTheDots_ sensor:
	<!-- mark:3 -->
	````C#
	// Hard coding guid for sensors. Not an issue for this particular application which is meant for testing and demos
	List<ConnectTheDotsSensor> sensors = new List<ConnectTheDotsSensor> {
		new ConnectTheDotsSensor("2298a348-e2f9-4438-ab23-82a3930662ab", "Light", "L"),
		new ConnectTheDotsSensor("d93ffbab-7dff-440d-a9f0-5aa091630201", "Temperature", "C"),
	};
	````

16. Next, add the following code to the **TelemetryTimer_Tick** method to poll the data from the temperature sensor and send it to Azure.

	````C#
	// Light Sensor
	ConnectTheDotsSensor lSensor = ctdHelper.sensors.Find(item => item.measurename == "Light");
	lSensor.value = this.hat.GetLightLevel();

	this.ctdHelper.SendSensorData(lSensor);

	this.HelloMessage.Text = String.Format("Temperature: {0} °C, Light {1}", tSensor.value.ToString("N2"), lSensor.value.ToString("P2", System.Globalization.CultureInfo.InvariantCulture));

	System.Diagnostics.Debug.WriteLine("Temperature: {0} °C, Light {1}", tSensor.value.ToString("N2"), lSensor.value.ToString("P2", System.Globalization.CultureInfo.InvariantCulture));
	````

	> **Note:** If you want to poll the Light sensor data at a different rate, you will need to create another _DispatchTimer_ and set it to different interval.

	After running the app you will see the following output in the debug console. In this case two messages are sent to Azure in every timer tick:

	![Debug console after adding Light Sensor](Images/debug-console-after-adding-light-sensor.png?raw=true)

	_Output Window after Adding the Light Sensor_

17. (Optional) If you have a screen connected to the Raspberry Pi device, you can improve the existing UI to show the sensor information in the screen. To do that, replace the content of the **MainPage.xaml** file code for this one:

	````XAML
	<Page
		x:Class="WindowsIoTCorePi2FezHat.MainPage"
		xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
		xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
		xmlns:local="using:WindowsIoTCorePi2FezHat"
		xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
		xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
		mc:Ignorable="d" Loaded="Page_Loaded">
		<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
			<StackPanel HorizontalAlignment="Center" VerticalAlignment="Center" Orientation="Horizontal">
				<StackPanel Width="100">
					<TextBlock Text="Light: " FontSize="26.667" Margin="10" />
					<TextBlock Text="Temp: " FontSize="26.667" Margin="10" />
				</StackPanel>
				<StackPanel>
					<StackPanel Margin="10">
					    <TextBlock Name="LightTextBox" FontSize="26.667" />
					    <ProgressBar Name="LightProgress" Value="0" Minimum="0" Maximum="1" Width="150"></ProgressBar>
					</StackPanel>
					<TextBlock Name="TempTextBox" FontSize="26.667" Margin="10" />
				</StackPanel>
			</StackPanel>
		</Grid>
	</Page>
	````

	The preceding view code incorporates a new textbox for every sensor and also a progress bar to graphically show the value of the Light sensor.

18. Lastly, you will update the **TelemetryTimer_Tick** method to adapt it to the new UI structure. Replace the method with the following code:

	````C#
	private void TelemetryTimer_Tick(object sender, object e)
	{
	    // Light Sensor
	    ConnectTheDotsSensor lSensor = ctdHelper.sensors.Find(item => item.measurename == "Light");
	    lSensor.value = this.hat.GetLightLevel();

	    this.ctdHelper.SendSensorData(lSensor);
	    this.LightTextBox.Text = lSensor.value.ToString("P2", System.Globalization.CultureInfo.InvariantCulture);
	    this.LightProgress.Value = lSensor.value;

	    // Temperature Sensor
	    var tSensor = ctdHelper.sensors.Find(item => item.measurename == "Temperature");
	    tSensor.value = this.hat.GetTemperature();
	    this.ctdHelper.SendSensorData(tSensor);

	    this.TempTextBox.Text = tSensor.value.ToString("N2", System.Globalization.CultureInfo.InvariantCulture);

	    System.Diagnostics.Debug.WriteLine("Temperature: {0} °C, Light {1}", tSensor.value.ToString("N2", System.Globalization.CultureInfo.InvariantCulture), lSensor.value.ToString("P2", System.Globalization.CultureInfo.InvariantCulture));
	}
	````

	![App UI Screenshot](Images/app-ui-screenshot.png?raw=true)

	_Universal app UI_
