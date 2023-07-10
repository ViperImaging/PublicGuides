# Optris via ViperVision

- Optris folder structure
- Optris configuration files
- Connecting to Optris camera via USB
- Optris Logs

**Folder structure for Optris Configs and Calibration files**  
```
ViperConfigData
|__Configurations
    |__Optris
        |__21064060 (Camera serial number is where generic.xml file is located)
        |  |__generic.xml
        |
        |__Cali (Calibration files for all cameras go here)
            |__Around seven or so files per camera

```

## Configuration Files

To ensure the camera functions correctly, make sure the configuration files and calibration files are up to date and properly placed.

If you're adding an Optris camera for the first time, we recommend letting the software auto-generate the required folder structure by adding a camera. Once generated, perform the following steps:

1. Locate the `ViperConfigData/Configurations/Optris` directory.
2. Confirm that there are two folders in this directory:
   - A folder named after the camera's serial number. This folder contains the camera's configuration file, named "generic.xml," which specifies the location of calibration files and the camera's serial number.
   - The `Cali` folder, which contains calibration files for all cameras.

!(Config folder)[optris_config_location.png]

For versions older than 4.33.X, you will need to make edits to the config file.

1. Open the "generic.xml" file located in the folder with the camera's serial number using a text editor.
2. Update the `<serial>` tag to match the camera's serial number.
3. Update the `<calipath>` tag with the location of the calibration files, such as "C:\ViperConfigData\Optris\Cali".

## Connecting to the Optris Camera via USB

To connect and set up the Optris camera, follow these steps:

1. Plug in the Optris camera via USB.
2. Open the camera discovery feature in the software. The connected cameras will appear as "Optris USB Imager" camera followed by vendor ID, manufacturer number, and serial number information. Note: The card in the discovery window is a placeholder and can be used for any Optris camera.
!(Camera discovery)[optris_usb_discovery.png]
3. Select any camera from the list and add it. A message will indicate that the camera's calibration files need to be in the specified config folder located at `ViperConfigData/Configurations/Optris/Cali`. Make sure you have the calibration files, they can be obtained from the usb that comes with your Optris camera or you can download via Optris Pix Connect software. 
4. Provide a name for your camera and enter its serial number. If the `Optris` config folder doesn't exist, it will be created. Add the calibration files to the `Cali` folder located at `ViperConfigData/Configurations/Optris/Cali`. There are usually around 7 files or so. The camera image will show up black if the calibration files are not present.
!(Adding camera)[entering_optris_serial.png]
!(Result if no cali files)[need_cali_files.png]
!(Calibration files)[cali_files.png]
5. For versions older than 4.33.X:
    - Modify the "generic.xml" file located in the camera's serial number folder.
    - Update the `<serial>` tag to match your camera's serial number.
    - Update the `<calipath>` tag to `ViperConfigData/Configurations/Optris/Cali`.

!(Config file)[generic_xml.png]

6. Restart the software, and the camera image should appear.

## Additional Logging Information for Optris Cameras

If you encounter issues with the Optris camera and find no relevant information in the ViperVision log files, follow these steps:

1. Check the installation path for a file named `ViperOptrisLogs.txt`.
2. This file contains Optris-specific logs that may help diagnose camera issues.


## Things to know

- The software does not have the features blocked out that are not used for the camera. 
- Camera functionality is limited to ROI's, Analysis, Processes, and Snapshots. The Xi400 has a focus feature and it is the only one that has that feature. We are blocking out the features of the camera that don't exist as we go since it would take about a month or two to block all those out we decided to do it over time. 
