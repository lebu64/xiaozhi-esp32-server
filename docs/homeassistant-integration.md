# Xiao Zhi ESP32 - Open Source Server and Home Assistant Integration Guide

[TOC]

-----

## Introduction

This document will guide you on how to integrate ESP32 devices with Home Assistant.

## Prerequisites

- Home Assistant is installed and configured
- The model I chose this time is: free ChatGLM, which supports function call function calls

## Operations Before Starting (Required)

### 1. Get HA Network Address Information

Please visit your Home Assistant network address. For example, my HA address is 192.168.4.7, port is default 8123, then open in browser:

```
http://192.168.4.7:8123
```

> Manual method to query HA IP address **(only applicable when Xiao Zhi esp32-server and HA are deployed on the same network device [e.g., same wifi])**:
>
> 1. Enter Home Assistant (frontend).
>
> 2. Click bottom left **Settings** → **System** → **Network**.
>
> 3. Scroll to the bottom `Home Assistant website` area, in the `local network` section, click the `eye` button to see the currently used IP address (such as `192.168.1.10`) and network interface. Click `copy link` to directly copy.
>
>    ![image-20250504051716417](images/image-ha-integration-01.png)

Or, if you have already set up a directly accessible Home Assistant OAuth address, you can also directly access in browser:

```
http://homeassistant.local:8123
```

### 2. Log in to `Home Assistant` to Get Development Key

Log in to `HomeAssistant`, click `Bottom left avatar -> Personal`, switch to `Security` navigation bar, scroll to bottom `Long-term Access Tokens` to generate api_key, and copy and save it. Subsequent methods will need to use this api key and it only appears once (small tip: You can save the generated QR code image, and later scan the QR code to extract the api key again).

## Method 1: HA Calling Function Co-built by Xiao Zhi Community

### Function Description

- If you need to add new devices later, this method requires manually restarting the `xiaozhi-esp32-server service` to update device information **(Important)**.

- You need to ensure that you have integrated `Xiaomi Home` in HomeAssistant and imported Mi Home devices into `HomeAssistant`.

- You need to ensure that the `xiaozhi-esp32-server control panel` can be used normally.

- My `xiaozhi-esp32-server control panel` and `HomeAssistant` are deployed on the same machine on another port, version is `0.3.10`

  ```
  http://192.168.4.7:8002
  ```

### Configuration Steps

#### 1. Log in to `HomeAssistant` to Organize Device List to Control

Log in to `HomeAssistant`, click `Settings at bottom left`, then enter `Devices & Services`, then click `Entities` at the top.

Then search for the switches you want to control in entities. After results appear, in the list, click one of the results, which will bring up a switch interface.

In the switch interface, we try clicking the switch to see if it turns on/off with our clicks. If it can be operated, it means it's normally connected to the network.

Then find the settings button in the switch panel, click it, and you can view the `Entity Identifier` of this switch.

We open a notepad and organize one piece of data in this format:

Location + English comma + Device name + English comma + `Entity Identifier` + English semicolon

For example, I'm at the company, I have a toy light, its identifier is switch.cuco_cn_460494544_cp1_on_p_2_1, then write this piece of data:

```
Company,Toy Light,switch.cuco_cn_460494544_cp1_on_p_2_1;
```

Of course, finally I might operate two lights, my final result is:

```
Company,Toy Light,switch.cuco_cn_460494544_cp1_on_p_2_1;
Company,Desk Lamp,switch.iot_cn_831898993_socn1_on_p_2_1;
```

This string of characters, we call it "Device List String" and need to save it well, it will be useful later.

#### 2. Log in to `Control Panel`

![image-20250504051716417](images/image-ha-integration-06.png)

Use administrator account to log in to `Control Panel`. In `Agent Management`, find your agent, then click `Configure Role`.

Set intent recognition to `Function Call` or `LLM Intent Recognition`. At this point you'll see an `Edit Functions` on the right. Click the `Edit Functions` button, which will pop up the `Function Management` dialog.

In the `Function Management` dialog, you need to check `HomeAssistant Device Status Query` and `HomeAssistant Device Status Modification`.

After checking, click `HomeAssistant Device Status Query` in `Selected Functions`, then configure your `HomeAssistant` address, key, and device list string in `Parameter Configuration`.

After editing, click `Save Configuration`, at this point the `Function Management` dialog will hide, then you click save agent configuration.

After successful save, you can wake up the device for operation.

#### 3. Wake Up Device for Control

Try saying to esp32, "Turn on XXX light"

## Method 2: Xiao Zhi Uses Home Assistant's Voice Assistant as LLM Tool

### Function Description

- This method has a relatively serious drawback - **this method cannot use the function_call plugin capabilities of Xiao Zhi open source ecosystem**, because using Home Assistant as Xiao Zhi's LLM tool transfers intent recognition capability to Home Assistant. But **this method can experience native Home Assistant operation functions, and Xiao Zhi's chat capability remains unchanged**. If you really mind this, you can use [Method 3](##Method 3: Using Home Assistant's MCP Service (Recommended)) which is also supported by Home Assistant, which can maximize the experience of Home Assistant functions.

### Configuration Steps:

#### 1. Configure Home Assistant's Large Model Voice Assistant.

**You need to configure Home Assistant's voice assistant or large model tool in advance.**

#### 2. Get Home Assistant's Voice Assistant Agent ID.

1. Enter Home Assistant page. Click `Developer Tools` on the left.
2. In the opened `Developer Tools`, click the `Actions` tab (as shown in operation 1), in the `Actions` option bar on the page, find or enter `conversation.process` and select `Conversation: Process` (as shown in operation 2).

![image-20250504043539343](images/image-ha-integration-02.png)

3. Check the `Agent` option on the page, in the constantly lit `Conversation Agent` select the voice assistant name you configured in step one, as shown, what I configured is `ZhipuAi` and select.

![image-20250504043854760](images/image-ha-integration-03.png)

4. After selecting, click `Switch to YAML mode` at the bottom left of the form.

![image-20250504043951126](images/image-ha-integration-04.png)

5. Copy the agent-id value, for example in the figure mine is `01JP2DYMBDF7F4ZA2DMCF2AGX2` (for reference only).

![image-20250504044046466](images/image-ha-integration-05.png)

6. Switch to Xiao Zhi open source server `xiaozhi-esp32-server`'s `config.yaml` file, in the LLM configuration, find Home Assistant, set your Home Assistant network address, Api key and the agent_id just queried.
7. Modify the `LLM` of the `selected_module` attribute in the `config.yaml` file to `HomeAssistant`, and `Intent` to `nointent`.
8. Restart Xiao Zhi open source server `xiaozhi-esp32-server` to use normally.

## Method 3: Using Home Assistant's MCP Service (Recommended)

### Function Description

- You need to integrate and install HA integration - [Model Context Protocol Server](https://www.home-assistant.io/integrations/mcp_server/) in Home Assistant in advance.

- This method and Method 2 are both official HA solutions. Different from Method 2, you can normally use the open source co-built plugins of Xiao Zhi open source server `xiaozhi-esp32-server`, while allowing you to freely use any LLM large model that supports function_call function.

### Configuration Steps

#### 1. Install Home Assistant's MCP Service Integration.

Integration official website - [Model Context Protocol Server](https://www.home-assistant.io/integrations/mcp_server/)..

Or follow the manual operations below.

> - Go to Home Assistant page **[Settings > Devices & Services](https://my.home-assistant.io/redirect/integrations)**.
>
> - In the bottom right corner, select **[Add Integration](https://my.home-assistant.io/redirect/config_flow_start?domain=mcp_server)** button.
>
> - Select **Model Context Protocol Server** from the list.
>
> - Follow the on-screen instructions to complete setup.

#### 2. Configure Xiao Zhi Open Source Server MCP Configuration Information

Enter the `data` directory, find the `.mcp_server_settings.json` file.

If there is no `.mcp_server_settings.json` file under your `data` directory,
- Please copy the `mcp_server_settings.json` file from the `xiaozhi-server` folder root directory to the `data` directory, and rename it to `.mcp_server_settings.json`
- Or [download this file](https://github.com/xinnan-tech/xiaozhi-esp32-server/blob/main/main/xiaozhi-server/mcp_server_settings.json), download to the `data` directory, and rename it to `.mcp_server_settings.json`

Modify this part of the content in `"mcpServers"`:

```json
"Home Assistant": {
      "command": "mcp-proxy",
      "args": [
        "http://YOUR_HA_HOST/mcp_server/sse"
      ],
      "env": {
        "API_ACCESS_TOKEN": "YOUR_API_ACCESS_TOKEN"
      }
},
```

Note:

1. **Replace Configuration:**
   - Replace `YOUR_HA_HOST` in `args` with your HA service address. If your service address already contains https/http (e.g., `http://192.168.1.101:8123`), then only enter `192.168.1.101:8123`.
   - Replace `YOUR_API_ACCESS_TOKEN` in `API_ACCESS_TOKEN` in `env` with the development key api key you obtained earlier.
2. **If you add configuration is inside the `"mcpServers"` brackets and there are no new `mcpServers` configurations afterwards, you need to remove the last comma `,`**, otherwise parsing may fail.

**Final effect reference (reference as follows)**:

```json
 "mcpServers": {
    "Home Assistant": {
      "command": "mcp-proxy",
      "args": [
        "http://192.168.1.101:8123/mcp_server/sse"
      ],
      "env": {
        "API_ACCESS_TOKEN": "abcd.efghi.jkl"
      }
    }
  }
```

#### 3. Configure Xiao Zhi Open Source Server System Configuration

1. **Select any LLM large model that supports function_call as Xiao Zhi's LLM chat assistant (but do not choose Home Assistant as LLM tool)**, this time I chose the model: free ChatGLM, which supports functioncall function calls, but sometimes calls are not very stable. If you want to pursue stability, it is recommended to set LLM to: DoubaoLLM, using the specific model_name: doubao-1-5-pro-32k-250115.

2. Switch to Xiao Zhi open source server `xiaozhi-esp32-server`'s `config.yaml` file, set your LLM large model configuration, and adjust the `Intent` of the `selected_module` configuration to `function_call`.

3. Restart Xiao Zhi open source server `xiaozhi-esp32-server` to use normally.
