# Working With Windows ISO Files


## How To Re-Create Bootable ISO From Extracted ISO Files

### Easy Method Using ImgBurn Third Party Application.

1. Download and run  [ImgBurn](https://www.imgburn.com/)
2. Click "Create Image file from files\\folders"
3. Under "Source", click folder with magnifying glass icon and browse to the extracted ISO folder where all installation files are
4. Under "Destination", write a path and a file name for you new ISO ending with .iso extension. Example: `C:\temp\win11_22H3_x64_engUS.iso`
5. Then on the right side, click "Advanced" tab, then "Bootable Disc" sub-tab bellow
6. Configure values like below:
	1. Click "Make Image Bootable" under "Emulation Type"
	2. Under "Boot Image" navigate to your extracted ISO folder, go to Boot subfolder and click ETFSBOOT.exe. It will look like `C:\your-extracted-iso-folder\BOOT\etfsboot.exe`
	3. Under "Sectors To Load" write: 8
	4. Other things leave as is
7. Click "Build" icon to start building ISO image

**Note: you will get warnings for incorrect UDF settings. Just accept "Yes" to automatically correct those. **

## How To Download Windows 10 Hidden ISO's

Since Microsoft is forcing Media Creation tool for preparing bootable drives for Windows installation, web page where ISOs are available won't present option to download the ISOs, just the Media Creation tool.

But in case you open the [download images site](https://www.microsoft.com/hr-hr/software-download/windows10) on mobile phones, those buttons will become available. So here we are going to force the mobile layout of the webpage to grant access to those download buttons.

## Procedure

1. Visit link below or google "download windows setup iso"
 https://www.microsoft.com/hr-hr/software-download/windows10

2. Open the webpage in a mobile layout
	* For Chrome, press F12 to get to the Developer Tools > toggle "mobile device" button > choose any mobile phone from the dropdown on the left > F5 to reload the page so you get it in the mobile layout.
	   
3. Chose Windows edition, language and architecture and proceed to download the image.