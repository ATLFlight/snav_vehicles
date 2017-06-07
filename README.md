# snav_vehciles
Snapdragon Navigator parameter files for different vehicle configurations

## TODO
- [ ] Snav params overview
- [ ] Fill in QDN links once available
- [ ] Contributing.md

## Getting Started with the Eco Drone (Name TBD)
#### Hardware
You'll need 3 pieces of hardware to begin

- [Ecodrone from World Electronics (Link TODO)]()
- [Snapdragon Flight Board](https://shop.intrinsyc.com/collections/product-development-kits/products/qualcomm-snapdragon-flight-sbc)
- [Snapdragon Flight ESC](https://shop.intrinsyc.com/collections/dragonboard-accessories/products/qualcomm-electronic-speed-control-board)

Once you have all the hardware, follow the instructions from World Electronics to assemble your drone [here (Link TODO)]()

#### Software
You will also need 3 deb files from QDN:
- [Snapdragon Navigator (Link TODO)]()
- [Snav ESC Firmware/Params (Link TODO)]()
- [Machine Vision Library (Link TODO)]()

#### License File
Lastly, you need a community license file from QDN: [License Request (Link TODO)]()



### Push your deb files

navigate to your downloaded deb files

```bash
adb push mv_0.9.1_8x74.deb /home/linaro/
adb push snav_1.2.31_8x74.deb /home/linaro/
adb push snav-esc_1.2.0.deb /home/linaro/
```

### Install the deb files

`adb shell`

```bash
dpkg -i mv_0.9.1_8x74.deb
dpkg -i snav_1.2.31_8x74.deb
dpkg -i snav-esc_1.2.0.deb
```

### Install License file

Navigate to your license file location and run:

```bash
adb push snapdragon-flight-license.bin /opt/qcom-licenses/
```

### Flash the ESC firmware/params

From this repo (snav_vehicles), you'll need to get the flash_esc_firmware.xml param file. Navigate to it and transfer it to your board:

```bash
adb push flash_esc_firmware.xml /usr/share/data/adsp/snav_params.xml
```

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

When snav starts, it will read the param file provided, which tells it to flash the ESC with the firmware provided in the deb file.  It will also use the ESC parameters provided for the drone's motor/prop set.

After the flashing it complete, you should hear TODO.  You can then stop snav with Ctrl-C

Now the vehicle is ready for its flight parameters. Transfer them to your board with:

```bash
adb push ecodrone.xml /usr/share/data/adsp/snav_params.xml
```

You can now either start snav via upstart:

```bash
sudo start snav
```

or simply restart your vehicle.  After that, it's time to start flying!  Reference the user guide next steps in flight testing.

