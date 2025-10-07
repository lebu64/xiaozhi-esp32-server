# MCP Endpoint Deployment Usage Guide

This tutorial contains 3 parts:
- 1. How to deploy the MCP endpoint service
- 2. How to configure MCP endpoint when using full module deployment
- 3. How to configure MCP endpoint when using single module deployment

# 1. How to Deploy the MCP Endpoint Service

## Step 1: Download MCP Endpoint Project Source Code

Open [MCP Endpoint Project Address](https://github.com/xinnan-tech/mcp-endpoint-server) in browser.

After opening, find a green button on the page that says `Code`, click it, then you'll see the `Download ZIP` button.

Click it to download the project source code zip file. After downloading to your computer, extract it. At this point, its name might be `mcp-endpoint-server-main`
You need to rename it to `mcp-endpoint-server`.

## Step 2: Start Program
This project is a very simple project, recommended to run using docker. However, if you don't want to use docker to run, you can refer to [this page](https://github.com/xinnan-tech/mcp-endpoint-server/blob/main/README_dev.md) to run using source code. The following is the docker running method:

```
# Enter this project source code root directory
cd mcp-endpoint-server

# Clear cache
docker compose -f docker-compose.yml down
docker stop mcp-endpoint-server
docker rm mcp-endpoint-server
docker rmi ghcr.nju.edu.cn/xinnan-tech/mcp-endpoint-server:latest

# Start docker container
docker compose -f docker-compose.yml up -d
# View logs
docker logs -f mcp-endpoint-server
```

At this point, the logs will output logs similar to the following:
```
250705 INFO-=====The following addresses are control panel/single module MCP endpoint addresses====
250705 INFO-Control Panel MCP Parameter Configuration: http://172.22.0.2:8004/mcp_endpoint/health?key=abc
250705 INFO-Single Module Deployment MCP Endpoint: ws://172.22.0.2:8004/mcp_endpoint/mcp/?token=def
250705 INFO-=====Please choose according to specific deployment, do not leak to anyone======
```

Please copy out the two interface addresses:

Since you are using docker deployment, absolutely do not directly use the above addresses!

Since you are using docker deployment, absolutely do not directly use the above addresses!

Since you are using docker deployment, absolutely do not directly use the above addresses!

First copy the addresses and put them in a draft. You need to know what your computer's LAN IP is. For example, my computer's LAN IP is `192.168.1.25`, then
Originally my interface addresses:
```
Control Panel MCP Parameter Configuration: http://172.22.0.2:8004/mcp_endpoint/health?key=abc
Single Module Deployment MCP Endpoint: ws://172.22.0.2:8004/mcp_endpoint/mcp/?token=def
```
Need to be changed to:
```
Control Panel MCP Parameter Configuration: http://192.168.1.25:8004/mcp_endpoint/health?key=abc
Single Module Deployment MCP Endpoint: ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=def
```

After changing, please use browser to directly access `Control Panel MCP Parameter Configuration`. When the browser displays code similar to this, it means success:
```
{"result":{"status":"success","connections":{"tool_connections":0,"robot_connections":0,"total_connections":0}},"error":null,"id":null,"jsonrpc":"2.0"}
```

Please keep the above two `interface addresses` well, they will be needed in the next step.

# 2. How to Configure MCP Endpoint When Using Full Module Deployment

If you are using full module deployment, use administrator account to log in to the control panel, click top `Parameter Dictionary`, select `Parameter Management` function.

Then search for parameter `server.mcp_endpoint`. At this point, its value should be `null` value.
Click the modify button, paste the `Control Panel MCP Parameter Configuration` obtained in the previous step into the `Parameter Value`. Then save.

If it can save successfully, it means everything is going smoothly, you can go to the agent to see the effect. If not successful, it means the control panel cannot access the mcp endpoint, most likely due to network firewall, or not filling in the correct LAN IP.

# 3. How to Configure MCP Endpoint When Using Single Module Deployment

If you are using single module deployment, find your configuration file `data/.config.yaml`.
Search for `mcp_endpoint` in the configuration file. If not found, you add the `mcp_endpoint` configuration. Similar to me it would be like this:
```
server:
  websocket: ws://your ip or domain:port number/xiaozhi/v1/
  http_port: 8002
log:
  log_level: INFO

# There may be more configurations here..

mcp_endpoint: your endpoint websocket address
```
At this point, please paste the `Single Module Deployment MCP Endpoint` obtained in `How to Deploy the MCP Endpoint Service` into `mcp_endpoint`. Similar to this:

```
server:
  websocket: ws://your ip or domain:port number/xiaozhi/v1/
  http_port: 8002
log:
  log_level: INFO

# There may be more configurations here

mcp_endpoint: ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=def
```

After configuration, starting the single module will output logs similar to the following:
```
250705[__main__]-INFO-Initialized component: vad successful SileroVAD
250705[__main__]-INFO-Initialized component: asr successful FunASRServer
250705[__main__]-INFO-OTA interface is          http://192.168.1.25:8002/xiaozhi/ota/
250705[__main__]-INFO-Vision analysis interface is     http://192.168.1.25:8002/mcp/vision/explain
250705[__main__]-INFO-mcp endpoint is        ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
250705[__main__]-INFO-Websocket address is    ws://192.168.1.25:8000/xiaozhi/v1/
250705[__main__]-INFO-=======The above address is websocket protocol address, do not access with browser=======
250705[__main__]-INFO-If you want to test websocket, please open test_page.html in the test directory with Google browser
250705[__main__]-INFO-=============================================================
```

As above, if it can output similar `mcp endpoint is` with `ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc`, it means the configuration is successful.
