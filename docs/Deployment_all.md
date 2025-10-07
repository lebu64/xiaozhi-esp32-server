# Deployment Architecture Diagram
![Please refer to the full module installation architecture diagram](../docs/images/deploy2.png)

# Method 1: Docker Running Full Modules
Starting from version `0.8.2`, the docker images released by this project only support `x86 architecture`. If you need to deploy on `arm64 architecture` CPUs, you can compile `arm64 images` locally according to [this tutorial](docker-build.md).

## 1. Install Docker

If your computer doesn't have docker installed yet, you can install it following the tutorial here: [docker installation](https://www.runoob.com/docker/ubuntu-docker-install.html)

There are two ways to install the full modules with docker. You can [use the lazy script](./Deployment_all.md#11-lazy-script) (author [@VanillaNahida](https://github.com/VanillaNahida))  
The script will automatically download the required files and configuration files for you. You can also use [manual deployment](./Deployment_all.md#12-manual-deployment) to build from scratch.

### 1.1 Lazy Script
Simple deployment, you can refer to the [video tutorial](https://www.bilibili.com/video/BV17bbvzHExd/). The text version tutorial is as follows:

> [!NOTE]  
> Currently only supports one-click deployment on Ubuntu servers. Other systems haven't been tested and may have some strange bugs.

Connect to the server using SSH tools and execute the following script with root privileges:
```bash
sudo bash -c "$(wget -qO- https://ghfast.top/https://raw.githubusercontent.com/xinnan-tech/xiaozhi-esp32-server/main/docker-setup.sh)"
```

The script will automatically complete the following operations:
> 1. Install Docker
> 2. Configure image sources
> 3. Download/pull images
> 4. Download speech recognition model files
> 5. Guide configuration of the server

After execution and simple configuration, refer to [4. Run Program](#4-run-program) and [5. Restart xiaozhi-esp32-server](#5-restart-xiaozhi-esp32-server) for the three most important things. After completing these three configurations, you can start using it.

### 1.2 Manual Deployment

#### 1.2.1 Create Directory

After installation, you need to find a directory to place the configuration files for this project. For example, we can create a new folder called `xiaozhi-server`.

After creating the directory, you need to create `data` folder and `models` folder under `xiaozhi-server`, and create `SenseVoiceSmall` folder under `models`.

The final directory structure should look like this:

```
xiaozhi-server
  ├─ data
  ├─ models
     ├─ SenseVoiceSmall
```

#### 1.2.2 Download Speech Recognition Model Files

This project's speech recognition model uses the `SenseVoiceSmall` model by default for speech-to-text conversion. Because the model is large, it needs to be downloaded separately. After downloading, place the `model.pt` file in the `models/SenseVoiceSmall` directory. Choose one of the following two download routes:

- Route 1: Alibaba ModelScope download [SenseVoiceSmall](https://modelscope.cn/models/iic/SenseVoiceSmall/resolve/master/model.pt)
- Route 2: Baidu Netdisk download [SenseVoiceSmall](https://pan.baidu.com/share/init?surl=QlgM58FHhYv1tFnUT_A8Sg&pwd=qvna) Extraction code: `qvna`

#### 1.2.3 Download Configuration Files

You need to download two configuration files: `docker-compose_all.yaml` and `config_from_api.yaml`. These files need to be downloaded from the project repository.

##### 1.2.3.1 Download docker-compose_all.yaml

Open [this link](../main/xiaozhi-server/docker-compose_all.yml) in your browser.

On the right side of the page, find the button named `RAW`. Next to the `RAW` button, find the download icon and click the download button to download the `docker-compose_all.yml` file. Download the file to your `xiaozhi-server` directory.

Or directly execute `wget https://raw.githubusercontent.com/xinnan-tech/xiaozhi-esp32-server/refs/heads/main/main/xiaozhi-server/docker-compose_all.yml` to download.

After downloading, return to this tutorial and continue.

##### 1.2.3.2 Download config_from_api.yaml

Open [this link](../main/xiaozhi-server/config_from_api.yaml) in your browser.

On the right side of the page, find the button named `RAW`. Next to the `RAW` button, find the download icon and click the download button to download the `config_from_api.yaml` file. Download the file to the `data` folder under your `xiaozhi-server`, then rename the `config_from_api.yaml` file to `.config.yaml`.

Or directly execute `wget https://raw.githubusercontent.com/xinnan-tech/xiaozhi-esp32-server/refs/heads/main/main/xiaozhi-server/config_from_api.yaml` to download and save.

After downloading the configuration files, let's confirm that the files in the entire `xiaozhi-server` are as follows:

```
xiaozhi-server
  ├─ docker-compose_all.yml
  ├─ data
    ├─ .config.yaml
  ├─ models
     ├─ SenseVoiceSmall
       ├─ model.pt
```

If your file directory structure is the same as above, continue. If not, carefully check if you missed any operations.

## 2. Backup Data

If you have successfully run the control panel before and it contains your key information, please copy important data from the control panel first. Because during the upgrade process, original data might be overwritten.

## 3. Clear Historical Version Images and Containers
Next, open the command line tool, use `Terminal` or `Command Line` tool to enter your `xiaozhi-server`, and execute the following commands:

```
docker compose -f docker-compose_all.yml down

docker stop xiaozhi-esp32-server
docker rm xiaozhi-esp32-server

docker stop xiaozhi-esp32-server-web
docker rm xiaozhi-esp32-server-web

docker stop xiaozhi-esp32-server-db
docker rm xiaozhi-esp32-server-db

docker stop xiaozhi-esp32-server-redis
docker rm xiaozhi-esp32-server-redis

docker rmi ghcr.nju.edu.cn/xinnan-tech/xiaozhi-esp32-server:server_latest
docker rmi ghcr.nju.edu.cn/xinnan-tech/xiaozhi-esp32-server:web_latest
```

## 4. Run Program
Execute the following command to start the new version container:

```
docker compose -f docker-compose_all.yml up -d
```

After execution, execute the following command to view log information:

```
docker logs -f xiaozhi-esp32-server-web
```

When you see output logs, it means your `Control Panel` has started successfully:

```
2025-xx-xx 22:11:12.445 [main] INFO  c.a.d.s.b.a.DruidDataSourceAutoConfigure - Init DruidDataSource
2025-xx-xx 21:28:53.873 [main] INFO  xiaozhi.AdminApplication - Started AdminApplication in 16.057 seconds (process running for 17.941)
http://localhost:8002/xiaozhi/doc.html
```

Please note that at this moment only the `Control Panel` can run. If port 8000 `xiaozhi-esp32-server` reports errors, ignore them for now.

At this point, you need to use a browser to open the `Control Panel` at: http://127.0.0.1:8002 and register the first user. The first user is the super administrator, and subsequent users are ordinary users. Ordinary users can only bind devices and configure agents; super administrators can perform model management, user management, parameter configuration, and other functions.

Next, do three important things:

### First Important Thing

Use the super administrator account to log in to the control panel, find `Parameter Management` in the top menu, find the first data in the list with parameter code `server.secret`, and copy its `Parameter Value`.

`server.secret` needs to be explained: this `Parameter Value` is very important. Its function is to allow our `Server` end to connect to `manager-api`. `server.secret` is a randomly generated key each time the manager module is deployed from scratch.

After copying the `Parameter Value`, open the `.config.yaml` file in the `data` directory under `xiaozhi-server`. Your configuration file content should look like this at this moment:

```
manager-api:
  url:  http://127.0.0.1:8002/xiaozhi
  secret: your server.secret value
```
1. Copy the `server.secret` `Parameter Value` you just copied from the `Control Panel` to the `secret` in the `.config.yaml` file.

2. Because you are using docker deployment, change the `url` to the following: `http://xiaozhi-esp32-server-web:8002/xiaozhi`

3. Because you are using docker deployment, change the `url` to the following: `http://xiaozhi-esp32-server-web:8002/xiaozhi`

4. Because you are using docker deployment, change the `url` to the following: `http://xiaozhi-esp32-server-web:8002/xiaozhi`

Similar to this effect:
```
manager-api:
  url: http://xiaozhi-esp32-server-web:8002/xiaozhi
  secret: 12345678-xxxx-xxxx-xxxx-123456789000
```

After saving, continue to do the second important thing.

### Second Important Thing

Use the super administrator account to log in to the control panel, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button. After the modification dialog pops up, fill in the key you registered with `Zhipu AI` into the `API Key` field. Then click save.

## 5. Restart xiaozhi-esp32-server

Next, open the command line tool, use `Terminal` or `Command Line` tool and enter:
```
docker restart xiaozhi-esp32-server
docker logs -f xiaozhi-esp32-server
```
If you can see logs similar to the following, it indicates that the Server has started successfully:

```
25-02-23 12:01:09[core.websocket_server] - INFO - Websocket address is      ws://xxx.xx.xx.xx:8000/xiaozhi/v1/
25-02-23 12:01:09[core.websocket_server] - INFO - =======The above address is websocket protocol address, do not access with browser=======
25-02-23 12:01:09[core.websocket_server] - INFO - If you want to test websocket, please open test_page.html in the test directory with Google browser
25-02-23 12:01:09[core.websocket_server] - INFO - =======================================================
```

Since you are deploying full modules, you have two important interfaces that need to be written to esp32.

OTA interface:
```
http://your host machine's LAN ip:8002/xiaozhi/ota/
```

Websocket interface:
```
ws://your host machine's ip:8000/xiaozhi/v1/
```

### Third Important Thing

Use the super administrator account to log in to the control panel, find `Parameter Management` in the top menu, find the parameter code `server.websocket`, and enter your `Websocket interface`.

Use the super administrator account to log in to the control panel, find `Parameter Management` in the top menu, find the parameter code `server.ota`, and enter your `OTA interface`.

Next, you can start operating your esp32 device. You can either `compile esp32 firmware yourself` or configure using `Xia Ge's compiled firmware version 1.6.1 or above`. Choose one of the two:

1. [Compile your own esp32 firmware](firmware-build.md).

2. [Configure custom server based on Xia Ge's compiled firmware](firmware-setting.md).

# Method 2: Local Source Code Running Full Modules

## 1. Install MySQL Database

If MySQL is already installed on your machine, you can directly create a database named `xiaozhi_esp32_server` in the database.

```sql
CREATE DATABASE xiaozhi_esp32_server CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

If you don't have MySQL yet, you can install mysql via docker:

```
docker run --name xiaozhi-esp32-server-db -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -e MYSQL_DATABASE=xiaozhi_esp32_server -e MYSQL_INITDB_ARGS="--character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci" -e TZ=Asia/Shanghai -d mysql:latest
```

## 2. Install Redis

If you don't have Redis yet, you can install redis via docker:

```
docker run --name xiaozhi-esp32-server-redis -d -p 6379:6379 redis
```

## 3. Run manager-api Program

3.1 Install JDK21, set JDK environment variables

3.2 Install Maven, set Maven environment variables

3.3 Use Vscode programming tool, install Java environment related plugins

3.4 Use Vscode programming tool to load manager-api module

Configure database connection information in `src/main/resources/application-dev.yml`:

```
spring:
  datasource:
    username: root
    password: 123456
```
Configure Redis connection information in `src/main/resources/application-dev.yml`:
```
spring:
    data:
      redis:
        host: localhost
        port: 6379
        password:
        database: 0
```

3.5 Run main program

This project is a SpringBoot project. The startup method is:
Open `Application.java` and run the `Main` method to start:

```
Path address:
src/main/java/xiaozhi/AdminApplication.java
```

When you see output logs, it means your `manager-api` has started successfully:

```
2025-xx-xx 22:11:12.445 [main] INFO  c.a.d.s.b.a.DruidDataSourceAutoConfigure - Init DruidDataSource
2025-xx-xx 21:28:53.873 [main] INFO  xiaozhi.AdminApplication - Started AdminApplication in 16.057 seconds (process running for 17.941)
http://localhost:8002/xiaozhi/doc.html
```

## 4. Run manager-web Program

4.1 Install nodejs

4.2 Use Vscode programming tool to load manager-web module

Use terminal command to enter manager-web directory:

```
npm install
```
Then start:
```
npm run serve
```

Please note, if your manager-api interface is not at `http://localhost:8002`, please modify the path in `main/manager-web/.env.development` during development.

After successful operation, you need to use a browser to open the `Control Panel` at: http://127.0.0.1:8001 and register the first user. The first user is the super administrator, and subsequent users are ordinary users. Ordinary users can only bind devices and configure agents; super administrators can perform model management, user management, parameter configuration, and other functions.

Important: After successful registration, use the super administrator account to log in to the control panel, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button. After the modification dialog pops up, fill in the key you registered with `Zhipu AI` into the `API Key` field. Then click save.

Important: After successful registration, use the super administrator account to log in to the control panel, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button. After the modification dialog pops up, fill in the key you registered with `Zhipu AI` into the `API Key` field. Then click save.

Important: After successful registration, use the super administrator account to log in to the control panel, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button. After the modification dialog pops up, fill in the key you registered with `Zhipu AI` into the `API Key` field. Then click save.

## 5. Install Python Environment

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
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-for
