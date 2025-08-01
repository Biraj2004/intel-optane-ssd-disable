# Make Intel Optane SSDs Hackintosh Compatible

### 1. Required Software

- Xiasl-Win64 [Here](https://github.com/ic005k/Xiasl/releases/download/1.1.67/Xiasl-Win64.zip)
- OCAT-Win64 [Here](https://github.com/ic005k/OCAuxiliaryTools/releases/download/20250001/OCAT-Win64.zip)
- Windows Device Manager (for hardware inspection)
- Working OpenCore EFI
- Laptop Used - ASUS-Vivobook-Gaming (F571GT)

Intel® Optane™ SSDs, especially the H10 variant, present compatibility issues in Hackintosh setups due to their hybrid structure (Optane + NVMe).
This guide walks you through identifying the device, patching using ACPI SSDTs, and integrating the changes with OpenCore using OCAT.

### 2. Identifying the Optane SSD

- Intel® Optane™ exists on two devices, i.e. one device with two drives present at the same time.

- First open **Device Manager** and find **Disk Drives** and **Storage Controllers**.

  <img src="https://i.postimg.cc/WzKbqVRd/20-July-02-36-48-PM-vlc.jpg" style="zoom: 80%;" />

  <img src="https://i.postimg.cc/HsV9q9Bz/20-July-02-38-04-PM-vlc.jpg" style="zoom: 68%;" />

- All we need to do is find out which of these two devices is Intel® Optane™ Memory.

- In Disk Drives, generally the one ending with O is the Optane. (*In our case - INTEL HBRPEKNX0202AO*). See and note the last two digits of the Value (*In our case it's EA*).

  <img src="https://i.postimg.cc/tTFFqBLQ/20-July-02-35-06-PM-vlc.jpg" alt="Last Known Parent" style="zoom: 68%;" />

- Once you get the name of the parent object, you can identify which storage controller is associated with it.

### 3. Identify Storage Controller and get BIOS device-path

- Again Open Device Manager.

- Go to Storage Controllers -> Select the first Standard NVM Express Controller -> Details

- Click ***last known parent*** property and Check the last two digits of the value and make sure it matches with the one of Disk Drives. (*In our case it's EA*)

  <img src="https://i.postimg.cc/gkm4TNDQ/20-July-03-25-58-PM-vlc.jpg" alt="Storage NVME Controller" style="zoom:67%;" />

- Now, once we identified the correct one, in the same Standard NVM Express Controller, now copy the BIOS id value from ***BIOS device name*** property in the similar way.

  <img src="https://i.postimg.cc/d3DYGr1V/20-July-03-47-06-PM-vlc.jpg" alt="BIOS id" style="zoom: 80%;" />

### 4. Writing methods for the Device

- The binary renaming of ACPI is not covered here.

  > ⚠️ **IMPORTANT**  
  > If your original DSDT table has a `_DSM` method already defined for that device, then you need to change the original `_DSM` method to `XDSM` or something else using the binary rename method first — otherwise, two duplicate methods are not allowed!


  <img src="https://i.postimg.cc/jSZxGx7N/20-July-03-56-18-PM-vlc.jpg" alt="dsl to aml;" />

- Open the software *Xiasl-Win64* and load the *SSDT-Optane-Disable* file into it.  [File Here](https://github.com/Biraj2004/intel-optane-ssd-disable/blob/main/SSDT-Optane-Disable.aml)

- Paste the copied BIOS-id value and replace the existing BIOS-id with the new one. Save it.

- Finally, compile to get your *.aml* patch.

### 5. ACPI Final Steps

- Go to `OpCore-Simplify -> Results -> EFI -> OC -> ACPI`

- Delete *SSDT-Disable_NVMe_RP11.aml* & *SSDT-Disable_NVMe_RP09.aml*

- Now copy the created *SSDT-Optane-Disable.aml* and paste it into the ACPI folder -

  ![Pasting the patch](https://i.postimg.cc/RZyfnxMk/20-July-04-07-27-PM-vlc.jpg)

### 6. Finalizing using OCAT (OpenCore Auxiliary Tool)

- Open OCAT and load the *config.plist* from `OpCore-Simplify -> Results -> EFI -> OC`

- In ACPI freshly add the *SSDT-Optane-Disable.aml* that we created and set it's value to `true` also check the box.

  ![True-Disable](https://i.postimg.cc/ZRcXqcrM/20-July-04-26-19-PM-vlc.jpg)

### Save it and exit. Enjoy!!

---

## Credits

- Original patching method inspired by various Hackintosh community forums and CSDN Blog.
- Written & compiled by [Biraj Sarkar](https://github.com/Biraj2004)

---

## Contributing

Have a better method or updated patch? Feel free to fork and submit a PR. Let's make Hackintosh easier for everyone.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## ⭐ Show Your Support

If this guide helped you, consider starring the repo or sharing it with others in the Hackintosh community!
