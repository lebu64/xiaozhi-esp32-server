# Voiceprint Recognition Enablement Guide

This tutorial contains 3 parts:
- 1. How to deploy the voiceprint recognition service
- 2. How to configure voiceprint recognition interface when using full module deployment
- 3. How to configure voiceprint recognition when using minimal deployment

# 1. How to Deploy the Voiceprint Recognition Service

## Step 1: Download Voiceprint Recognition Project Source Code

Open [Voiceprint Recognition Project Address](https://github.com/xinnan-tech/voiceprint-api) in browser.

After opening, find a green button on the page that says `Code`, click it, then you'll see the `Download ZIP` button.

Click it to download the project source code zip file. After downloading to your computer, extract it. At this point, its name might be `voiceprint-api-main`
You need to rename it to `voiceprint-api`.

## Step 2: Create Database and Tables

Voiceprint recognition requires dependency on `mysql` database. If you have previously deployed the `Control Panel`, it means you have already installed `mysql`. You can share it.

You can try using the `telnet` command on the host machine to see if you can normally access the `mysql` `3306` port.
```
telnet 127.0.0.1 3306
```
If you can access port 3306, please ignore the following content and go directly to step 3.

If you cannot access, you need to recall how your `mysql` was installed.

If your mysql was installed using an installation package yourself, it means your `mysql` has network isolation. You may need to solve the problem of accessing `mysql`'s `3306` port first.

If your `mysql` was installed through this project's `docker-compose_all.yml`. You need to find the `docker-compose_all.yml` file you used to create the database at that time, modify the following content:

Before modification:
```
  xiaozhi-esp32-server-db:
    ...
    networks:
      - default
    expose:
      - "3306:3306"
```

After modification:
```
  xiaozhi-esp32-server-db:
    ...
    networks:
      - default
    ports:
      - "3306:3306"
```

Note: Change `expose` under `xiaozhi-esp32-server-db` to `ports`. After modification, need to restart. The following are the commands to restart mysql:

```
# Enter the folder where your docker-compose_all.yml is located, for example mine is xiaozhi-server
cd xiaozhi-server
docker compose -f docker-compose_all.yml down
docker compose -f docker-compose.yml up -d
```

After starting, use the `telnet` command on the host machine again to see if you can normally access the `mysql` `3306` port.
```
telnet 127.0.0.1 3306
```
Normally this should allow access.

## Step 3: Create Database and Tables
If your host machine can normally access the mysql database, then create a database named `voiceprint_db` and `voiceprints` table on mysql.

```
CREATE DATABASE voiceprint_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

USE voiceprint_db;

CREATE TABLE voiceprints (
    id INT AUTO_INCREMENT PRIMARY KEY,
    speaker_id VARCHAR(255) NOT NULL UNIQUE,
    feature_vector LONGBLOB NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_speaker_id (speaker_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## Step 4: Configure Database Connection

Enter the `voiceprint-api` folder, create a folder named `data`.

Copy `voiceprint.yaml` from the `voiceprint-api` root directory to the `data` folder, rename it to `.voiceprint.yaml`

Next, you need to focus on configuring the database connection in `.voiceprint.yaml`.

```
mysql:
  host: "127.0.0.1"
  port: 3306
  user: "root"
  password: "your_password"
  database: "voiceprint_db"
```

Note! Since your voiceprint recognition service is deployed using docker, `host` needs to be filled with your `LAN IP of the machine where mysql is located`.

Note! Since your voiceprint recognition service is deployed using docker, `host` needs to be filled with your `LAN IP of the machine where mysql is located`.

Note! Since your voiceprint recognition service is deployed using docker, `host` needs to be filled with your `LAN IP of the machine where mysql is located`.

## Step 5: Start Program
This project is a very simple project, recommended to run using docker. However, if you don't want to use docker to run, you can refer to [this page](https://github.com/xinnan-tech/voiceprint-api/blob/main/README.md) to run using source code. The following is the docker running method:

```
# Enter this project source code root directory
cd voiceprint-api

# Clear cache
docker compose -f docker-compose.yml down
docker stop voiceprint-api
docker rm voiceprint-api
docker rmi ghcr.nju.edu.cn/xinnan-tech/voiceprint-api:latest

# Start docker container
docker compose -f docker-compose.yml up -d
# View logs
docker logs -f voiceprint-api
```

At this point, the logs will output logs similar to the following:
```
250711 INFO-🚀 Start: Production environment service startup (Uvicorn), listening address: 0.0.0.0:8005
250711 INFO-============================================================
250711 INFO-Voiceprint interface address: http://127.0.0.1:8005/voiceprint/health?key=abcd
250711 INFO-============================================================
```

Please copy out the voiceprint interface address:

Since you are using docker deployment, absolutely do not directly use the above address!

Since you are using docker deployment, absolutely do not directly use the above address!

Since you are using docker deployment, absolutely do not directly use the above address!

First copy the address and put it in a draft. You need to know what your computer's LAN IP is. For example, my computer's LAN IP is `192.168.1.25`, then
Originally my interface address:
```
http://127.0.0.1:8005/voiceprint/health?key=abcd
```
Need to be changed to:
```
http://192.168.1.25:8005/voiceprint/health?key=abcd
```

After changing, please use browser to directly access the `voiceprint interface address`. When the browser displays code similar to this, it means success:
```
{"total_voiceprints":0,"status":"healthy"}
```

Please keep the modified `voiceprint interface address` well, it will be needed in the next step.

# 2. How to Configure Voiceprint Recognition When Using Full Module Deployment

## Step 1: Configure Interface
If you are using full module deployment, use administrator account to log in to the control panel, click top `Parameter Dictionary`, select `Parameter Management` function.

Then search for parameter `server.voice_print`. At this point, its value should be `null` value.
Click the modify button, paste the `voiceprint interface address` obtained in the previous step into the `Parameter Value`. Then save.

If it can save successfully, it means everything is going smoothly, you can go to the agent to see the effect. If not successful, it means the control panel cannot access the voiceprint recognition, most likely due to network firewall, or not filling in the correct LAN IP.

## Step 2: Set Agent Memory Mode

Enter your agent's role configuration, set memory to `Local Short-term Memory`, must enable `Report Text + Voice`.

## Step 3: Chat with Your Agent

Power on your device, then chat with it using normal speech speed and tone.

## Step 4: Set Voiceprint

In the control panel, `Agent Management` page, in the agent panel, there is a `Voiceprint Recognition` button, click it. At the bottom there is an `Add Button`. You can register voiceprint for someone's speech.
In the pop-up box, it is recommended to fill in the `Description` attribute, which can be the person's occupation, personality, hobbies. Convenient for the agent to analyze and understand the speaker.

## Step 5: Chat with Your Agent

Power on your device, ask it, "Do you know who I am?" If it can answer correctly, it means the voiceprint recognition function is working normally.

# 3. How to Configure Voiceprint Recognition When Using Minimal Deployment

## Step 1: Configure Interface
Open the `xiaozhi-server/data/.config.yaml` file (create if doesn't exist), then add/modify the following content:

```
# Voiceprint recognition configuration
voiceprint:
  # Voiceprint interface address
  url: your voiceprint interface address
  # Speaker configuration: speaker_id,name,description
  speakers:
    - "test1,张三,张三是一个程序员"
    - "test2,李四,李四是一个产品经理"
    - "test3,王五,王五是一个设计师"
```

Paste the `voiceprint interface address` obtained in the previous step into `url`. Then save.

Add `speakers` parameter as needed. Here you need to pay attention to this `speaker_id` parameter, it will be used later for voiceprint registration.

## Step 2: Register Voiceprint
If you have already started the voiceprint service, access `http://localhost:8005/voiceprint/docs` in local browser to view API documentation, here only explains how to use the voiceprint registration API.

The voiceprint registration API address is `http://localhost:8005/voiceprint/register`, request method is POST.

The request header needs to contain Bearer Token authentication, token is the part after `?key=` in the `voiceprint interface address`, for example if my voiceprint registration address is `http://127.0.0.1:8005/voiceprint/health?key=abcd`, then my token is `abcd`.

The request body contains speaker ID (speaker_id), and WAV audio file (file), request example as follows:

```
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -F "speaker_id=your_speaker_id_here" \
  -F "file=@/path/to/your/file" \
  http://localhost:8005/voiceprint/register
```

Here `file` is the audio file of the speaker's speech to be registered, `speaker_id` needs to be consistent with the `speaker_id` in the first step interface configuration. For example, if I need to register Zhang San's voiceprint, the `speaker_id` filled in `.config.yaml` for Zhang San is `test1`, then when I register Zhang San's voiceprint, the `speaker_id` filled in the request body is `test1`, `file` is the audio file of Zhang San speaking a paragraph.

## Step 3: Start Service

Start Xiao Zhi server and voiceprint service, then you can use normally.
