---
layout: post
title: "DMU Hackers Digispark Tutorial"
date: 2026-01-12 12:00:00 +0000
visible: false
categories: [security, offensive-security, research]
description: "A step-by-step guide to creating and using a Digispark for offensive security testing."
---

# Getting Started with Digispark (USB HID Attacks)

This tutorial walks through setting up a Digispark from scratch and using it to perform basic USB HID keystroke injection. It covers drivers, Arduino IDE setup, writing your first payload, and common pitfalls.

This is intended for learning and experimentation on systems you own or are authorised to test.

---

## What is a Digispark?

A Digispark is a very small development board based on the ATtiny85 microcontroller. Despite its size and cost (often under £2), it can emulate a USB keyboard and inject keystrokes into a target system.

Unlike purpose-built tools such as the USB Rubber Ducky, a Digispark:
- uses software USB rather than a dedicated USB controller
- has extremely limited memory
- is less reliable and more timing-sensitive

These limitations make it an excellent learning tool because they force you to understand how HID attacks actually work.

---

## What you’ll need

- A Digispark board (ATtiny85 based)
- A Windows machine for development
- Arduino IDE

---

## Step 1: Install Digispark USB drivers (Windows)

Windows does not include drivers for Digispark out of the box.

1. Download the Digispark driver: [https://www.hattonsec.com/assets/files/dmu-hackers-digispark/DPinst64.exe](/assets/files/dmu-hackers-digispark/DPinst64.exe)
2. Run `DPinst64.exe` as Administrator

---

## Step 2: Install the Arduino IDE (if not already installed)

### The Arduino IDE should already be installed on your machines. In which case, skip this step.

Download and install the Arduino IDE from the official Arduino website: [https://www.arduino.cc/en/software](https://www.arduino.cc/en/software)

During installation, allow:
- USB driver installation
- File associations

Once installed, launch the Arduino IDE.

---

## Step 3: Add Digispark board support

Digispark boards are not included by default in Arduino.

1. Open Arduino IDE
2. Go to File > Preferences
3. In Additional Boards Manager URLs, add:

   `https://www.hattonsec.com/assets/files/dmu-hackers-digispark/package_digistump_index.json`

4. Click OK
5. Go to Tools > Board > Boards Manager
6. Search for Digistump AVR Boards
7. Install the package

---

## Step 4: Configure Arduino for Digispark

Set the following board in the Arduino IDE:

- Tools > Board: `Digispark (Default - 16.5mhz)`
- Tools > Programmer: `Micronucleus`

## Step 5: Your first HID payload

This example opens a Youtube video.

Example code:

```cpp
#include "DigiKeyboard.h"
void setup() {
  // empty
}

void loop() 
{
  // Initialise DigiSpark
  DigiKeyboard.sendKeyStroke(0);

  // Hits Windows+R
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT);
  DigiKeyboard.delay(600);

  // Enters a YouTube link and presses enter
  DigiKeyboard.print("https://www.youtube.com/watch?v=KP4LoBFMNmw");
  DigiKeyboard.sendKeyStroke(KEY_ENTER);

  for (;;) {/* Stops the loop */}
}
```

---

## Step 6: Uploading to the Digispark

Uploading to a Digispark is different from most Arduino boards.

1. Click Verify (tick button) in the Arduino IDE and ensure there are no errors.
2. Click Upload (arrow button) in the Arduino IDE
3. Wait for the message: Plug in device now
4. Plug in the Digispark within 60 seconds
5. Wait for upload to complete ("Micronucleus done. Thank you!)
6. Your script should run automatically. You can unplug and plug the Digispark back in to run your script again.
7. If you wish to modify the script on your Digispark, unplug the Digispark, change your script, and start from step 1 again.

If it fails:
- Unplug and retry
- Ensure no other Digispark is plugged in
- Increase delays in your payload

---

## Understanding timing and reliability

Digispark HID attacks are extremely timing-sensitive.

The device:
- has no feedback channel
- cannot tell if a window is open
- executes everything blindly using delays

Most failures are caused by timing or focus issues.

---

## Keyboard layout issues (UK vs US)

Digispark sends key codes, not characters.

This means:
- Special characters often break
- Payloads written for US layouts may fail on UK systems

Avoid special characters where possible.

---

## Memory limitations

The ATtiny85 has:
- 8 KB flash memory total
- Approximately 5 to 6 KB available after USB and bootloader
- 512 bytes of RAM

Payloads must be short and simple.