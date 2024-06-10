# Disable CFG Lock on XPS 9300

What we are doing here is essentiall finding where the configuration value for CFG Lock is in the UEFI and setting it manually, as it isn't exposed by dell in the setup menu.

- Download UEFITool: https://github.com/LongSoft/UEFITool/releases/
- Downlaod IFRExtractor: https://github.com/LongSoft/IFRExtractor-RS/
- Download latest BIOS from Dells website
- Use the following command to extract:
`python3 Dell_PFS_Extract.py <BIOS_UPGRADE.EXE>`
- Open UEFITool and choose the largest BIN file in the extracted files
- Use CTRL+F to do a text search and search for "CFG Lock"
- Double click on the bottom of the window where it says "Unicode text "CFG Lock" found in PE32 image section at header-offset XXYYZZ"
- Right click on PE32 image section, select Extract as is and save the file with .bin extension. (e.g. Section_PE32_image_Setup.bin)
- In a terminal, use IFRxtractor to export strings from the exported PE32 bin:
`.\IFRExtractor Section_PE32_image_Setup.bin setup.txt`
- Open the text file in notepad and CTRL+F for "CFG Lock"
- You should find a string like:
`OneOf Prompt: "CFG Lock", Help: "Configure MSR 0xE2[15], CFG Lock bit", QuestionFlags: 0x10, QuestionId: 0x12A, VarStoreId: 0x3, VarOffset: 0x43, Flags: 0x10, Size: 8, Min: 0x0, Max: 0x1, Step: 0x0`
Note the VarOffset (0x43) and VarStoreId (0x3)
- Now you need to find the name of the setup. Scroll to the top of the text file and look for a line where the VarStoreId matches as follows:
`VarStoreEfi Guid: B08F97FF-E6E8-4193-A997-5E9E9B0ADB32, VarStoreId: 0x3, Attributes: 0x7, Size: 0x2A6, Name: "CpuSetup"`
Note how the VarStoreId is the same as before. the Name key is "CpuSetup", which is what we need.

- Now we can form the command to disable CFG Lock in the form  `setup_var.efi <OFFSET> <VALUE> -n <VAR_NAME> -r`

`setup_var.efi 0x43 0x0 -n CpuSetup -r`
- Copy setup_var.efi to the root of your bootable USB
- on the OpenCore Menu, press space and choose opencore shell
- Run the above command to disable CFG Lock
- Verify CFG Lock is off by pressing space and running ControlMsrE2.efi

Sources:
https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html
https://github.com/dreamwhite/bios-extraction-guide/tree/master/Dell