# Weather Plugin Usage Guide

## Overview

The weather plugin `get_weather` is one of the core features of the Xiaozhi ESP32 voice assistant, supporting voice queries for weather information across China. The plugin is based on the Hefeng Weather API, providing real-time weather and 7-day weather forecast functionality.

## API Key Application Guide

### 1. Register Hefeng Weather Account

1. Visit [Hefeng Weather Console](https://console.qweather.com/)
2. Register an account and complete email verification
3. Log in to the console

### 2. Create Application to Get API Key

1. After entering the console, click ["Project Management"](https://console.qweather.com/project?lang=zh) on the right → "Create Project"
2. Fill in project information:
   - **Project Name**: e.g., "Xiaozhi Voice Assistant"
3. Click Save
4. After project creation is complete, click "Create Credentials" in this project
5. Fill in credential information:
    - **Credential Name**: e.g., "Xiaozhi Voice Assistant"
    - **Authentication Method**: Select "API Key"
6. Click Save
7. Copy the `API Key` from the credentials - this is the first key configuration information

### 3. Get API Host

1. In the console, click ["Settings"](https://console.qweather.com/setting?lang=zh) → "API Host"
2. View the dedicated `API Host` address assigned to you - this is the second key configuration information

The above operations will yield two important configuration pieces of information: `API Key` and `API Host`

## Configuration Methods (Choose One)

### Method 1. If you use the Control Console deployment (Recommended)

1. Log in to the Control Console
2. Enter the "Role Configuration" page
3. Select the agent to configure
4. Click the "Edit Functions" button
5. Find the "Weather Query" plugin in the right parameter configuration area
6. Check "Weather Query"
7. Enter the first key configuration `API Key` copied earlier into `Weather Plugin API Key`
8. Enter the second key configuration `API Host` copied earlier into `Developer API Host`
9. Save the configuration, then save the agent configuration

### Method 2. If you only have single module xiaozhi-server deployment

Configure in `data/.config.yaml`:

1. Enter the first key configuration `API Key` copied earlier into `api_key`
2. Enter the second key configuration `API Host` copied earlier into `api_host`
3. Enter your city into `default_location`, for example `Guangzhou`

```yaml
plugins:
  get_weather:
    api_key: "Your Hefeng Weather API Key"
    api_host: "Your Hefeng Weather API Host Address"
    default_location: "Your default query city"
