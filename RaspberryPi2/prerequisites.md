#Prerequisites

1. [Download Windows 10 and Install Visual Studio 2015](#vs)
2. [Download and Install the Windows 10 IoT Core tools](#w10iot)
3. [(Optional) Install GitHub Desktop for Windows](#github)


<a name="vs"></a>
##1. Download Windows 10 and Install Visual Studio 2015
To setup your Windows 10 IoT Core development PC, you first need to install the following:

1.	**Make sure you are running the public release of Windows 10 (version 10.0.10240) or better.** You can upgrade from [here](http://www.microsoft.com/en-us/software-download/windows10). If you are already running Windows 10, you can find your current build number by clicking the start button, typing “winver”, and hitting enter.
2.	**Install Visual Studio 2015**
   1.	We recommend [Visual Studio Community Edition](http://go.microsoft.com/fwlink/?LinkID=534599), but Visual Studio Professional 2015 and Visual Studio Enterprise 2015 will work as well (available [here](http://go.microsoft.com/fwlink/?LinkID=534599)).
   2.	If you have to install Visual Studio, make sure to do a **Custom** install and select the checkbox **Universal Windows App Development Tools -> Tools and Windows SDK** and **Common Tools -> GitHub Extension for Visual Studio**.
   3.	**Validate your Visual Studio installation** by selecting Help -> About Microsoft Visual Studio. The required version of **Visual Studio** is **14.0.23107.0 D14Rel**. The required version of **Visual Studio Tools for Universal Windows Apps** is **14.0.23121.00 D14OOB**.
3.	**Install Windows IoT Core Project Templates** from [here](https://visualstudiogallery.msdn.microsoft.com/06507e74-41cf-47b2-b7fe-8a2624202d36). Alternatively, the templates can be found by searching for *Windows IoT Core Project Templates* in the [Visual Studio Gallery](https://visualstudiogallery.msdn.microsoft.com/) or directly from Visual Studio in the Extension and Updates dialog (Tools > Extensions and Updates > Online).
4.	**Enable developer mode** on your Windows 10 device by following [these instructions](https://msdn.microsoft.com/library/windows/apps/xaml/dn706236.aspx). The relevant portion of the linked instructions is the “Windows 10 Desktops/tablets” section, as you should be attempting setup with one of these devices.


<a name="w10iot"></a>
##2. Download and Install the Windows 10 IoT Core tools
The IoTCoreWatcher tool displays all the Windows 10 IoT Core devices on your network. Click [here](http://go.microsoft.com/fwlink/?LinkId=616847) to download it, and follow the instructions to install it. It should launch once it’s finished installing.

1.	[Download](http://go.microsoft.com/fwlink/?LinkId=616847) the ISO for the Raspberry Pi 2 from the Microsoft Download Center.
2.	**Save the ISO** to a local folder: ![](http://oliviak.blob.core.windows.net/blog/iot/4%201%20install%201.png)
3.	Double click on the ISO (IoT Core RPi.iso). It will automatically mount itself as a virtual drive so you can access the contents. ![](http://oliviak.blob.core.windows.net/blog/iot/4%201%20install%202.png)
4.	Install **Windows_10_IoT_Core_RPi2.msi**. When installation is complete, flash.ffu will be located at **C:\Program Files (x86)\Microsoft IoT\FFU\RaspberryPi2** ![](http://oliviak.blob.core.windows.net/blog/iot/4%201%20install%203.png)
5.	Eject the Virtual CD when done

<a name="github"></a>
##3. (Optional) Install GitHub Desktop for Windows
1.	Download [GitHub Desktop for Windows](https://desktop.github.com/).
2.	Set up an account on [GitHub](https://github.com/).
3.	Sign into GitHub Desktop with your GitHub account.
