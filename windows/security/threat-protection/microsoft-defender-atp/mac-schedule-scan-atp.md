---
title: How to schedule scans with MDATP for macOS
description: Learn how to schedule an automatic scanning time for Microsoft Defender ATP in macOS to better protect your organization's assets.
keywords: microsoft, defender, atp, mac, scans, antivirus
search.product: eADQiWindows 10XVcnh
search.appverid: met150
ms.prod: w10
ms.mktglfcycl: deploy
ms.sitesec: library
ms.pagetype: security
ms.author: dansimp
author: dansimp
ms.localizationpriority: medium
manager: dansimp
audience: ITPro
ms.collection: M365-security-compliance
ms.topic: conceptual
---

# Schedule scans with Microsoft Defender ATP for Mac

While you can start a threat scan at any time with Microsoft Defender ATP, your enterprise might benefit from scheduled or timed scans. For example, you can schedule a scan to run at the beginning of every workday or week. Create a scanning schedule using the *launchd* daemon on a macOS device.

## Schedule a scan with *launchd*

1. The following code shows the schema you need to use to schedule a scan. Open a text editor and use this example as a guide for your own scheduled scan file.

    For more information on the *.plist* file format used here, see [About Information Property List Files](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html) at the official Apple developer website.

    ```XML
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
      "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>Label</key>
        <string>com.microsoft.wdav.schedquickscan</string>
        <key>ProgramArguments</key>
        <array>
            <string>sh</string>
            <string>-c</string>
            <string>/usr/local/bin/mdatp --scan --quick</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
        <key>StartCalendarInterval</key>
        <dict>
            <key>Day</key>
            <integer>3</integer>
            <key>Hour</key>
            <integer>2</integer>
            <key>Minute</key>
            <integer>0</integer>
            <key>Weekday</key>
            <integer>5</integer>
        </dict>
        <key>StartInterval</key>
        <integer>604800</integer>
        <key>WorkingDirectory</key>
        <string>/usr/local/bin/</string>
    </dict>
    </plist>
     ```

2. Save the file as *com.microsoft.wdav.schedquickscan.plist*.

    > [!TIP]
    > To run a full scan instead of a quick scan, change line 8 to read `/usr/local/bin/mdatp --scan --full` and save the file as *com.microsoft.wdav.sched**full**scan.plist* instead of *com.microsoft.wdav.sched**quick**scan.plist*.

3. Open **Terminal**.
4. Enter the following commands to load your file:

    ```bash
    launchctl load /Library/LaunchDaemons/<your file name.plist>
    launchctl start <your file name>
    ```

5. Your scheduled scan will run at the date, time, and frequency you defined in your p-list. In the example, the scan runs at 2:00 AM every seven days on a Friday, with the StartInterval using 604,800 seconds for one week.

 > [!NOTE]
 > Agents executed with *launchd* will not run at the scheduled time while the computer is asleep. They will instead run once the device resumes from sleep mode.
 > 
 > If the device is turned off, the scan will run at the next scheduled scan time.
