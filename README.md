# snav_vehicles
Snapdragon Navigator parameter files for various vehicle configurations

## Getting Started with the Dragon Drone Development Kit
#### Hardware
You'll need 3 pieces of hardware to begin

- [Dragon Drone Development Kit](https://worldsway.com/product/dragon-drone-development-kit)
- [Snapdragon Flight Board](https://shop.intrinsyc.com/collections/product-development-kits/products/qualcomm-snapdragon-flight-sbc)
- [Snapdragon Flight ESC](https://shop.intrinsyc.com/collections/dragonboard-accessories/products/qualcomm-electronic-speed-control-board)
- Optionally, a Spektrum DX-8 and Remote Receiver can be very useful for development.

Once you have all the hardware, follow the instructions from World Electronics to assemble your drone [here](https://worldsway.com/wp-content/uploads/2017/08/DragonDDK-End-User-Assembly-Instructions_V2.pdf)

#### Software

Make sure that you have the most up-to-date platform and flight-controller addon.  Instructions can be found [here](https://github.com/ATLFlight/ATLFlightDocs/blob/master/PlatformGettingStarted.md).  These instructions were tested with platform and addon versions 3.1.3.1.

You will also need 3 deb files from QDN:
- [Snapdragon Navigator (under SDK)](https://developer.qualcomm.com/hardware/snapdragon-flight/sd-navigator)
- [Snav ESC Firmware/Params (under SDK)](https://developer.qualcomm.com/hardware/snapdragon-flight/sd-navigator)
- [Machine Vision Library (version 0.9.1)](https://developer.qualcomm.com/software/machine-vision-sdk/tools)

Note the currently supported software versions for the Dragon DDK are Snapdragon Navigator 1.2.31 and MV 0.9.1

Many of the below step require the use of adb ([Android Debug Bridge](https://developer.android.com/studio/command-line/adb.html)) for communication via usb.  Make sure that your Snapdragon Flight board is connected via usb and that you have adb installed.  The board will also need to be powered through the ESC.  Power over USB will not start the Snapdragon Flight board.

#### License File
Lastly, you need a community license file from QDN: [License Request](https://developer.qualcomm.com/hardware/snapdragon-flight/missing-key-req)

### Install the fc_addon
Unzip the fc_addon file and run the install script to push the necessary files via adb

```bash
unzip Flight_3.1.3.1_qcom_flight_controller_hexagon_sdk_add_on.zip
```

Linux/OSX:
```bash
./installfcaddon.sh
```

Windows:
```bash
installfcaddon.bat
```

### Push your deb files

Next, navigate to your downloaded deb files and transfer them to your board:

```bash
adb push mv_0.9.1_8x74.deb /home/linaro/
adb push snav_1.2.31_8x74.deb /home/linaro/
adb push snav-esc_1.2.0.deb /home/linaro/
```

### Install the deb files
To install the deb files, you will have to log into a shell session on the board:
`adb shell`

In that shell session, run the following commands. Run them individually, as you will need to type in "Yes" to agree to the license terms for each deb file.

```bash
cd /home/linaro
dpkg -i mv_0.9.1_8x74.deb
dpkg -i snav_1.2.31_8x74.deb
dpkg -i snav-esc_1.2.0.deb
```

To log out of the board, type Ctrl-D or type "exit"

### Install License file
Navigate to your license file location and run:

```bash
adb shell "mkdir -p /opt/qcom-licenses/"
adb push snapdragon-flight-license.bin /opt/qcom-licenses/
```

### Flash the ESC firmware/params

From this repo (snav_vehicles), you'll need to get the flash_esc_firmware.xml param file. Navigate to it and transfer it to your board:

```bash
adb push flash_esc_firmware.xml /usr/share/data/adsp/snav_params.xml
```

Log into the board:

```bash
adb shell
```

If you just installed snav and transferred you license, snav should not be running. To be certain, however, run:

```bash
sudo stop snav
```

Then manually start snav with:

```bash
sudo snav
```

When snav starts, it will read the param file provided, which tells it to flash the ESC with the firmware provided in the deb file.  It will also use the ESC parameters provided for the drone's motor/prop set.  This process will take approximately 30 seconds.  You should hear each of the four ESCs beep, one at a time.  After they are finished flashing, the motors will play a tone, and the rear LED with start flashing with the SNAV flight mode.  Now stop snav in the terminal with Ctrl-C.  Exit the adb shell (Ctrl-D or "exit").

Now the vehicle is ready for its flight parameters. Transfer them to your board with:

```bash
adb push dragonddk.xml /usr/share/data/adsp/snav_params.xml
```

You can now either start snav via upstart:

```bash
adb shell
sudo start snav
```

or simply restart your vehicle.  Snav should now successfully start, making a tone and flashing the LED.  The last thing to do is set up the vehicle to use VIO (Visual-Inertial Odometry) for flight control. Log into the board, navigate to /etc/snav, and run configure_vio.sh:

```bash
adb shell
cd /etc/snav
./configure_vio.sh -c downward
```

and lastly, do a static accelerometer calibration:

```bash
adb shell
snav_calibration_manager -s
```

After that, it's time to start flying!  Reference the user guide for next steps in flight testing.

