# Vision Model Usage Guide
This tutorial is divided into two parts:
- Part 1: Single module running xiaozhi-server to enable vision model
- Part 2: How to enable vision model when running full modules

Before enabling the vision model, you need to prepare three things:
- You need to prepare a device with a camera, and this device has already implemented camera calling functionality in Xia Ge's repository. For example, `LCSC·Practical ESP32-S3 Development Board`
- Your device firmware version is upgraded to 1.6.6 or above
- You have successfully run through the basic conversation module

## Single Module Running xiaozhi-server to Enable Vision Model

### Step 1: Confirm Network
Since the vision model will start port 8003 by default.

If you are running with docker, please confirm whether your `docker-compose.yml` has released port `8003`. If not, update to the latest `docker-compose.yml` file.

If you are running with source code, confirm whether the firewall has released port `8003`.

### Step 2: Choose Your Vision Model
Open your `data/.config.yaml` file, set your `selected_module.VLLM` to a certain vision model. Currently we already support vision models with `openai` type interfaces. `ChatGLMVLLM` is one of the models compatible with `openai`.

```
selected_module:
  VAD: ..
  ASR: ..
  LLM: ..
  VLLM: ChatGLMVLLM
  TTS: ..
  Memory: ..
  Intent: ..
```

Assuming we use `ChatGLMVLLM` as the vision model, then we need to first log in to the [Zhipu AI](https://bigmodel.cn/usercenter/proj-mgmt/apikeys) website to apply for a key. If you have already applied for a key before, you can reuse this key.

In your configuration file, add this configuration. If this configuration already exists, set your api_key.

```
VLLM:
  ChatGLMVLLM:
    api_key: your api_key
```

### Step 3: Start xiaozhi-server Service
If you are using source code, enter command to start:
```
python app.py
```
If you are running with docker, restart the container:
```
docker restart xiaozhi-esp32-server
```

After starting, it will output logs with the following content:

```
2025-06-01 **** - OTA interface is           http://192.168.4.7:8003/xiaozhi/ota/
2025-06-01 **** - Vision analysis interface is        http://192.168.4.7:8003/mcp/vision/explain
2025-06-01 **** - Websocket address is       ws://192.168.4.7:8000/xiaozhi/v1/
2025-06-01 **** - =======The above address is websocket protocol address, do not access with browser=======
2025-06-01 **** - If you want to test websocket, please open test_page.html in the test directory with Google browser
2025-06-01 **** - =============================================================
```

After starting, use browser to open the `vision analysis interface` link in the logs. See what is output? If you are on linux and don't have a browser, you can execute this command:
```
curl -i your vision analysis interface
```

Normally it will display like this:
```
MCP Vision interface is running normally, vision explanation interface address is: http://xxxx:8003/mcp/vision/explain
```

Please note, if you are deploying on public network, or using docker deployment, be sure to modify this configuration in your `data/.config.yaml`:
```
server:
  vision_explain: http://your ip or domain:port number/mcp/vision/explain
```

Why? Because the vision explanation interface needs to be delivered to the device. If your address is a LAN address, or a docker internal address, the device cannot access it.

Assuming your public network address is `111.111.111.111`, then `vision_explain` should be configured like this:

```
server:
  vision_explain: http://111.111.111.111:8003/mcp/vision/explain
```

If your MCP Vision interface is running normally, and you have also tried to access the delivered `vision explanation interface address` normally with browser, please continue to the next step.

### Step 4: Device Wake Up and Enable

Say to the device "Please turn on the camera, tell me what you see"

Pay attention to the xiaozhi-server log output, see if there are any errors.

## How to Enable Vision Model When Running Full Modules

### Step 1: Confirm Network
Since the vision model will start port 8003 by default.

If you are running with docker, please confirm whether your `docker-compose_all.yml` has mapped port `8003`. If not, update to the latest `docker-compose_all.yml` file.

If you are running with source code, confirm whether the firewall has released port `8003`.

### Step 2: Confirm Your Configuration File

Open your `data/.config.yaml` file, confirm whether your configuration file structure is the same as `data/config_from_api.yaml`. If not the same, or missing certain items, please complete them.

### Step 3: Configure Vision Model Key

Then we need to first log in to the [Zhipu AI](https://bigmodel.cn/usercenter/proj-mgmt/apikeys) website to apply for a key. If you have already applied for a key before, you can reuse this key.

Log in to the `Control Panel`, click `Model Configuration` in the top menu, click `Vision Large Language Model` in the left sidebar, find `VLLM_ChatGLMVLLM`, click the modify button, in the pop-up box, enter your key in `API Key`, click save.

After successful save, go to the agent you need to test, click `Configure Role`, in the opened content, check whether `Vision Large Language Model (VLLM)` has selected the vision model just now. Click save.

### Step 3: Start xiaozhi-server Module
If you are using source code, enter command to start:
```
python app.py
```
If you are running with docker, restart the container:
```
docker restart xiaozhi-esp32-server
```

After starting, it will output logs with the following content:

```
2025-06-01 **** - Vision analysis interface is        http://192.168.4.7:8003/mcp/vision/explain
2025-06-01 **** - Websocket address is       ws://192.168.4.7:8000/xiaozhi/v1/
2025-06-01 **** - =======The above address is websocket protocol address, do not access with browser=======
2025-06-01 **** - If you want to test websocket, please open test_page.html in the test directory with Google browser
2025-06-01 **** - =============================================================
```

After starting, use browser to open the `vision analysis interface` link in the logs. See what is output? If you are on linux and don't have a browser, you can execute this command:
```
curl -i your vision analysis interface
```

Normally it will display like this:
```
MCP Vision interface is running normally, vision explanation interface address is: http://xxxx:8003/mcp/vision/explain
```

Please note, if you are deploying on public network, or using docker deployment, be sure to modify this configuration in your `data/.config.yaml`:
```
server:
  vision_explain: http://your ip or domain:port number/mcp/vision/explain
```

Why? Because the vision explanation interface needs to be delivered to the device. If your address is a LAN address, or a docker internal address, the device cannot access it.

Assuming your public network address is `111.111.111.111`, then `vision_explain` should be configured like this:

```
server:
  vision_explain: http://111.111.111.111:8003/mcp/vision/explain
```

If your MCP Vision interface is running normally, and you have also tried to access the delivered `vision explanation interface address` normally with browser, please continue to the next step.

### Step 4: Device Wake Up and Enable

Say to the device "Please turn on the camera, tell me what you see"

Pay attention to the xiaozhi-server log output, see if there are any errors.
