# Lab 3: Read data from FEZ HAT sensors - Creating a Universal App
Now that the device is configured, you will see how to create an application to read the value of the FEZ HAT sensors, and then send those values to an Azure IoT Hub.

	![Lab 3 Overview](Images/3-HOL.png?raw=true)

	_Overview of Lab 3_

In order to get the information out of the hat sensors, you will take advantage of the [Developers' Guide](https://www.ghielectronics.com/docs/329/fez-hat-developers-guide "GHI Electronics FEZ HAT Developer's Guide") that [GHI Electronics](https://www.ghielectronics.com/ "GHI Electronics")  published.

1. Navigate to the folder **Code\GHIElectronics** and open the _GHIElectronics.UAP.sln_ solution file (You must have **Visual Studio** installed in order to open the solution).

2. After opening the solution you will see several projects. The _Developers's Guide_ comes with examples of many of the shields provided by the company. Right-click the one named _GHIElectronics.UAP.Examples.FEZHAT_, and select **Set as Startup Project**.

	![Set FEZ HAT examples project as default](Images/set-fez-hat-examples-project-as-default.png?raw=true)

	_Setting the FEZ hat example as the default project_

3. Ensure that the target platform for the project is set to "**ARM**":

	![arm-target-platform](Images/arm-target-platform.png?raw=true)

4. Build the solution to restore the NuGet packages, and make sure it builds:

	![ghifezhat-build-solution](Images/ghifezhat-build-solution.png?raw=true)

	![ghifezhat-build-succeeded](Images/ghifezhat-build-succeeded.png?raw=true)

5. If after building the solution you still see IntelliSense errors in the code, you may need to close and re-open Visual Studio, then re-open the solution.

	> **Note:** Now you will inspect the sample code to see how it works. Bear in mind that this example is intended to show all the available features of the shield, while in this lab you will use just a couple of them (temperature and light sensors).

6. Open the _MainPage.xaml.cs_ file and locate the **Setup** method.

	````C#
	private async void Setup()
	{
		this.hat = await GIS.FEZHAT.CreateAsync();

		this.hat.S1.SetLimits(500, 2400, 0, 180);
		this.hat.S2.SetLimits(500, 2400, 0, 180);

		this.timer = new DispatcherTimer();
		this.timer.Interval = TimeSpan.FromMilliseconds(100);
		this.timer.Tick += this.OnTick;
		this.timer.Start();
	}
	````

	In the first line, the program creates an instance of the FEZ HAT driver and stores it in a local variable. The driver is used for interacting with the shield. Then, after setting the limits for the servos (not used in this lab), a new **DispatchTimer** is created. A timer is often used in projects of this kind to poll the state of the sensors and perform operations. In this case the **OnTick** method is called every 100 miliseconds. You can see this method below.

	````C#
	private void OnTick(object sender, object e)
	{
		double x, y, z;

		this.hat.GetAcceleration(out x, out y, out z);

		this.LightTextBox.Text = this.hat.GetLightLevel().ToString("P2", CultureInfo.InvariantCulture);
		this.TempTextBox.Text = this.hat.GetTemperature().ToString("N2", CultureInfo.InvariantCulture);
		this.AccelTextBox.Text = $"({x:N2}, {y:N2}, {z:N2})";
		this.Button18TextBox.Text = this.hat.IsDIO18Pressed().ToString();
		this.Button22TextBox.Text = this.hat.IsDIO22Pressed().ToString();
		this.AnalogTextBox.Text = this.hat.ReadAnalog(GIS.FEZHAT.AnalogPin.Ain1).ToString("N2", CultureInfo.InvariantCulture);

		...
	}
	````
	This sample shows how to use the FEZ HAT driver to get data from the sensors. The data is then shown in the UI. (To see how the UI is designed check the _MainPage.xaml_ file)

7. To deploy the application to the Raspberry Pi, the device has to be on the same network as the development computer. To run the program, select **Remote device** in the _Debug Target_ dropdown list:

	![Deploy to Remote machine](Images/deploy-to-remote-machine.png?raw=true)

	_Deploying the application to a Remote Machine_

8. If a remote machine has not been selected before, the **Select Remote Connection** screen will be displayed:

	![Remote Connection](Images/remote-connection.png?raw=true)

	_Setting up the Remote Connection_

9. If the device is not auto-detected, the Raspberry Pi IP or name can be entered in the **Address** field. Otherwise, click the desired device. Change the **Authentication Mode** to **Universal (Unencrypted Protocol)**:

	![Set Authentication mode to Universal](Images/set-authentication-mode-to-universal.png?raw=true)

	_Setting the Authentication Mode_

10. If you want to change the registered remote device later it can be done in the project **Properties** page. Right-click the project name (_GHIElectronics.UAP.Examples.FEZHAT_) and select **Properties**. In the project Properties' page, select the **Debug** tab and enter the new device name or IP in the **Remote Machine** field.

	![Change Remote connection](Images/change-remote-connection.png?raw=true)

	_Changing the Remote Connection Settings_

	> **Note:** Clicking the **Find** button will display the **Remote Connection** screen.

11. If you don't have a screen connected to the _Raspberry_, you can add the following code to the **OnTick** method in order to show the value of the sensors in the Visual Studio **Output Console**.  (Insert the code after reading the sensors).

	````C#
	// Add diagnostics information
	System.Diagnostics.Debug.WriteLine("Light: {0}, Temp: {1}, Accel: {2}, Button18: {3}, Button22: {4}",
	    this.LightTextBox.Text,
	    this.TempTextBox.Text,
	    this.AccelTextBox.Text,
	    this.Button18TextBox.Text,
	    this.Button22TextBox.Text);
	````


12. Click the debug button to start the deployment to the Raspberry Pi.  The first deployment will take some time as the remote debug tools, frameworks, and your code all need to be deployed.  This could take up to a couple of minutes to completely deploy.  You can monitor the status in the Visual Studio "**Output**" window.

	![debug-ghifezhat](Images/debug-ghifezhat.png?raw=true)

13. If the program is successfully deployed to the device, the current value of the different sensors will be displayed on the screen. The shield leds will also be turned on and off alternately. In addition, if you added the Debug.Writeline code above to the OnTick method, the "**Output**" window will display sensor data:

	![ghifezhat-debug-output](Images/ghifezhat-debug-output.png?raw=true)
