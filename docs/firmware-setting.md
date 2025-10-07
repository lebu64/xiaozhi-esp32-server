# Configure Custom Server Based on Xia Ge's Compiled Firmware

## Step 1: Confirm Version
Flash Xia Ge's already compiled [firmware version 1.6.1 or above](https://github.com/78/xiaozhi-esp32/releases)

## Step 2: Prepare Your OTA Address
If you followed the tutorial and are using full module deployment, you should have an OTA address.

At this point, please open your OTA address in your browser, for example my OTA address:
```
https://2662r3426b.vicp.fun/xiaozhi/ota/
```

If it displays "OTA interface is running normally, websocket cluster count: X". Then proceed.

If it displays "OTA interface is not running normally", it's probably because you haven't configured the `Websocket` address in the `Control Panel`. Then:

- 1. Log in to the control panel as super administrator

- 2. Click `Parameter Management` in the top menu

- 3. Find the `server.websocket` item in the list, and enter your `Websocket` address. For example, mine would be:

```
wss://2662r3426b.vicp.fun/xiaozhi/v1/
```

After configuration, refresh your OTA interface address in the browser to see if it's normal. If it's still not normal, confirm again whether Websocket has started normally and whether the Websocket address has been configured.

## Step 3: Enter Network Configuration Mode
Enter the machine's network configuration mode, click "Advanced Options" at the top of the page, enter your server's `ota` address inside, click save. Restart the device.
![Please refer to - OTA address setting](../docs/images/firmware-setting-ota.png)

## Step 4: Wake Up Xiao Zhi, Check Log Output

Wake up Xiao Zhi, check if the logs are outputting normally.

## Common Questions
Here are some common questions for reference:

[1. Why does Xiao Zhi recognize my speech as Korean, Japanese, English?](./FAQ.md)

[2. Why does "TTS task error file does not exist" occur?](./FAQ.md)

[3. TTS often fails, often times out](./FAQ.md)

[4. Can connect to self-built server using Wifi, but cannot connect in 4G mode](./FAQ.md)

[5. How to improve Xiao Zhi's conversation response speed?](./FAQ.md)

[6. I speak slowly, Xiao Zhi keeps interrupting during pauses](./FAQ.md)

[7. I want to control lights, air conditioners, remote power on/off, etc. through Xiao Zhi](./FAQ.md)
