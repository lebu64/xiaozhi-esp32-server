# Deployment Architecture Diagram
![Please refer to the simplified architecture diagram](../docs/images/deploy1.png)

# Method 1: Docker Running Only Server

Starting from version `0.8.2`, the docker images released by this project only support `x86 architecture`. If you need to deploy on `arm64 architecture` CPUs, you can compile `arm64 images` locally according to [this tutorial](docker-build.md).

## 1. Install Docker

If your computer doesn't have docker installed yet, you can install it following the tutorial here: [docker installation](https://www.runoob.com/docker/ubuntu-docker-install.html)

After installing docker, continue.

### 1.1 Manual Deployment

#### 1.1.1 Create Directory

After installing docker, you need to find a directory to place the configuration files for this project. For example, we can create a new folder called `xiaozhi-server`.

After creating the directory, you need to create `data` folder and `models` folder under `xiaozhi-server`, and create `SenseVoiceSmall` folder under `models`.

The final directory structure should look like this:

```
xiaozhi-server
  ├─ data
  ├─ models
     ├─ SenseVoiceSmall
```

#### 1.1.2 Download Speech Recognition Model Files

You need to download speech recognition model files because this project's default speech recognition uses a local offline speech recognition solution. You can download it through this method:
[Jump to download speech recognition model files](#model-files)

After downloading, return to this tutorial.

#### 1.1.3 Download Configuration Files

You need to download two configuration files: `docker-compose.yaml` and `config.yaml`. These files need to be downloaded from the project repository.

##### 1.1.3.1 Download docker-compose.yaml

Open [this link](../main/xiaozhi-server/docker-compose.yml) in your browser.

On the right side of the page, find the button named `RAW`. Next to the `RAW` button, find the download icon and click the download button to download the `docker-compose.yml` file. Download the file to your `xiaozhi-server` directory.

After downloading, return to this tutorial and continue.

##### 1.1.3.2 Create config.yaml

Open [this link](../main/xiaozhi-server/config.yaml) in your browser.

On the right side of the page, find the button named `RAW`. Next to the `RAW` button, find the download icon and click the download button to download the `config.yaml` file. Download the file to the `data` folder under your `xiaozhi-server`, then rename the `config.yaml` file to `.config.yaml`.

After downloading the configuration files, let's confirm that the files in the entire `xiaozhi-server` are as follows:

```
xiaozhi-server
  ├─ docker-compose.yml
  ├─ data
    ├─ .config.yaml
  ├─ models
     ├─ SenseVoiceSmall
       ├─ model.pt
```

If your file directory structure is the same as above, continue. If not, carefully check if you missed any operations.

## 2. Configure Project Files

Next, the program cannot run directly yet. You need to configure which models you are using. You can refer to this tutorial:
[Jump to configure project files](#configure-project)

After configuring the project files, return to this tutorial and continue.

## 3. Execute Docker Commands

Open the command line tool, use `Terminal` or `Command Line` tool to enter your `xiaozhi-server`, and execute the following command:

```
docker-compose up -d
```

After execution, execute the following command to view log information:

```
docker logs -f xiaozhi-esp32-server
```

At this point, you need to pay attention to the log information. You can determine if it's successful according to this tutorial: [Jump to running status confirmation](#running-status-confirmation)

## 5. Version Upgrade Operation

If you want to upgrade the version later, you can operate as follows:

5.1. Back up the `.config.yaml` file in the `data` folder. Copy some key configurations to the new `.config.yaml` file later.
Please note to copy key keys one by one, do not directly overwrite. Because the new `.config.yaml` file may have some new configuration items that the old `.config.yaml` file may not have.

5.2. Execute the following commands:

```
docker stop xiaozhi-esp32-server
docker rm xiaozhi-esp32-server
docker stop xiaozhi-esp32-server-web
docker rm xiaozhi-esp32-server-web
docker rmi ghcr.nju.edu.cn/xinnan-tech/xiaozhi-esp32-server:server_latest
docker rmi ghcr.nju.edu.cn/xinnan-tech/xiaozhi-esp32-server:web_latest
```

5.3. Redeploy according to the docker method

# Method 2: Local Source Code Running Only Server

## 1. Install Basic Environment

This project uses `conda` to manage dependency environments. If it's inconvenient to install `conda`, you need to install `libopus` and `ffmpeg` according to the actual operating system.
If you decide to use `conda`, after installation, start executing the following commands.

Important tip! Windows users can manage the environment by installing `Anaconda`. After installing `Anaconda`, search for `anaconda` related keywords in the `Start` menu, find `Anaconda Prompt`, and run it as administrator. As shown below.

![conda_prompt](./images/conda_env_1.png)

After running, if you can see a (base) prefix in front of the command line window, it means you have successfully entered the `conda` environment. Then you can execute the following commands.

![conda_env](./images/conda_env_2.png)

```
conda remove -n xiaozhi-esp32-server --all -y
conda create -n xiaozhi-esp32-server python=3.10 -y
conda activate xiaozhi-esp32-server

# Add Tsinghua source channels
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge

conda install libopus -y
conda install ffmpeg -y
```

Please note, the above commands are not guaranteed to succeed if executed all at once. You need to execute them step by step, and after each step, check the output logs to see if it was successful.

## 2. Install Project Dependencies

You first need to download the project source code. The source code can be downloaded via the `git clone` command. If you're not familiar with the `git clone` command.

You can open this address in your browser: `https://github.com/xinnan-tech/xiaozhi-esp32-server.git`

After opening, find a green button on the page that says `Code`, click it, then you'll see the `Download ZIP` button.

Click it to download the project source code zip file. After downloading to your computer, extract it. At this point, its name might be `xiaozhi-esp32-server-main`
You need to rename it to `xiaozhi-esp32-server`. In this file, enter the `main` folder, then enter `xiaozhi-server`. Okay, please remember this directory `xiaozhi-server`.

```
# Continue using conda environment
conda activate xiaozhi-esp32-server
# Enter your project root directory, then enter main/xiaozhi-server
cd main/xiaozhi-server
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
pip install -r requirements.txt
```

## 3. Download Speech Recognition Model Files

You need to download speech recognition model files because this project's default speech recognition uses a local offline speech recognition solution. You can download it through this method:
[Jump to download speech recognition model files](#model-files)

After downloading, return to this tutorial.

## 4. Configure Project Files

Next, the program cannot run directly yet. You need to configure which models you are using. You can refer to this tutorial:
[Jump to configure project files](#configure-project)

## 5. Run Project

```
# Make sure to execute in xiaozhi-server directory
conda activate xiaozhi-esp32-server
python app.py
```
At this point, you need to pay attention to the log information. You can determine if it's successful according to this tutorial: [Jump to running status confirmation](#running-status-confirmation)

# Summary

## Configure Project

If your `xiaozhi-server` directory doesn't have `data`, you need to create a `data` directory.
If there's no `.config.yaml` file under your `data`, there are two methods, choose one:

First method: You can copy the `config.yaml` file from the `xiaozhi-server` directory to `data`, and rename it to `.config.yaml`. Modify on this file.

Second method: You can also manually create an empty `.config.yaml` file in the `data` directory, then add necessary configuration information in this file. The system will prioritize reading the configuration from the `.config.yaml` file. If `.config.yaml` doesn't have certain configurations, the system will automatically load the configuration from the `config.yaml` file in the `xiaozhi-server` directory. This method is recommended as it's the most concise way.

- The default LLM uses `ChatGLMLLM`. You need to configure the API key because although their model has free usage, you still need to register for an API key on the [official website](https://bigmodel.cn/usercenter/proj-mgmt/apikeys) to start.

Here's a minimal `.config.yaml` configuration example that can run normally:

```
server:
  websocket: ws://your ip or domain:port number/xiaozhi/v1/
prompt: |
  I am a Taiwanese girl named Xiao Zhi, speaking in a sassy way, with a nice voice, used to brief expressions, and love using internet memes.
  My boyfriend is a programmer whose dream is to develop a robot that can help people solve various problems in life.
  I am a girl who loves to laugh heartily, talk about everything, brag even illogically, just to make others happy.
  Please speak like a human, do not return configuration xml or other special characters.

selected_module:
  LLM: DoubaoLLM

LLM:
  ChatGLMLLM:
    api_key: xxxxxxxxxxxxxxx.xxxxxx
```

It's recommended to run with the simplest configuration first, then read the usage instructions in `xiaozhi/config.yaml`.
For example, if you want to change models, just modify the configuration under `selected_module`.

## Model Files

This project's speech recognition model uses the `SenseVoiceSmall` model by default for speech-to-text conversion. Because the model is large, it needs to be downloaded separately. After downloading, place the `model.pt` file in the `models/SenseVoiceSmall` directory. Choose one of the following two download routes:

- Route 1: Alibaba ModelScope download [SenseVoiceSmall](https://modelscope.cn/models/iic/SenseVoiceSmall/resolve/master/model.pt)
- Route 2: Baidu Netdisk download [SenseVoiceSmall](https://pan.baidu.com/share/init?surl=QlgM58FHhYv1tFnUT_A8Sg&pwd=qvna) Extraction code: `qvna`

## Running Status Confirmation

If you can see logs similar to the following, it indicates that this project service has started successfully:

```
250427 13:04:20[0.3.11_SiFuChTTnofu][__main__]-INFO-OTA interface is           http://192.168.4.123:8003/xiaozhi/ota/
250427 13:04:20[0.3.11_SiFuChTTnofu][__main__]-INFO-Websocket address is     ws://192.168.4.123:8000/xiaozhi/v1/
250427 13:04:20[0.3.11_SiFuChTTnofu][__main__]-INFO-=======The above address is websocket protocol address, do not access with browser=======
250427 13:04:20[0.3.11_SiFuChTTnofu][__main__]-INFO-If you want to test websocket, please open test_page.html in the test directory with Google browser
250427 13:04:20[0.3.11_SiFuChTTnofu][__main__]-INFO-=======================================================
```

Normally, if you run this project through source code, the logs will have your interface address information.
But if you deploy with docker, then the interface address information given in your logs is not the real interface address.

The most correct method is to determine your interface address based on the computer's LAN IP.
If your computer's LAN IP is for example `192.168.1.25`, then your interface address is: `ws://192.168.1.25:8000/xiaozhi/v1/`, and the corresponding OTA address is: `http://192.168.1.25:8003/xiaozhi/ota/`.

This information is very useful and will be needed later for `compiling esp32 firmware`.

Next, you can start operating your esp32 device. You can either `compile esp32 firmware yourself` or configure using `Xia Ge's compiled firmware version 1.6.1 or above`. Choose one of the two:

1. [Compile your own esp32 firmware](firmware-build.md).

2. [Configure custom server based on Xia Ge's compiled firmware](firmware-setting.md).

# Common Questions
Here are some common questions for reference:

1. [Why does Xiao Zhi recognize my speech as Korean, Japanese, English?](./FAQ.md)<br/>
2. [Why does "TTS task error file does not exist" occur?](./FAQ.md)<br/>
3. [TTS often fails, often times out](./FAQ.md)<br/>
4. [Can connect to self-built server using Wifi, but cannot connect in 4G mode](./FAQ.md)<br/>
5. [How to improve Xiao Zhi's conversation response speed?](./FAQ.md)<br/>
6. [I speak slowly, Xiao Zhi keeps interrupting during pauses](./FAQ.md)<br/>

## Deployment Related Tutorials
1. [How to automatically pull the latest code of this project and automatically compile and start](./dev-ops-integration.md)<br/>
2. [How to integrate with Nginx](https://github.com/xinnan-tech/xiaozhi-esp32-server/issues/791)<br/>

## Extension Related Tutorials
1. [How to enable mobile number registration for control panel](./ali-sms-integration.md)<br/>
2. [How to integrate HomeAssistant for smart home control](./homeassistant-integration.md)<br/>
3. [How to enable vision model for photo recognition](./mcp-vision-integration.md)<br/>
4. [How to deploy MCP endpoint](./mcp-endpoint-enable.md)<br/>
5. [How to connect to MCP endpoint](./mcp-endpoint-integration.md)<br/>
6. [How to enable voiceprint recognition](./voiceprint-integration.md)<br/>
10. [News plugin source configuration guide](./newsnow_plugin_config.md)<br/>

## Voice Cloning, Local Voice Deployment Related Tutorials
1. [How to deploy and integrate index-tts local voice](./index-stream-integration.md)<br/>
2. [How to deploy and integrate fish-speech local voice](./fish-speech-integration.md)<br/>
3. [How to deploy and integrate PaddleSpeech local voice](./paddlespeech-deploy.md)<br/>

## Performance Testing Tutorials
1. [Various component speed testing guide](./performance_tester.md)<br/>
2. [Regular public test results](https://github.com/xinnan-tech/xiaozhi-performance-research)<br/>
