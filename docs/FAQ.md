# Frequently Asked Questions ❓

### 1. Why does Xiao Zhi recognize my speech as Korean, Japanese, English? 🇰🇷

Suggestion: Check if `models/SenseVoiceSmall` already has the `model.pt` file. If not, you need to download it. Check here: [Download Speech Recognition Model Files](Deployment.md#model-files)

### 2. Why does "TTS task error file does not exist" occur? 📁

Suggestion: Check if you have correctly installed the `libopus` and `ffmpeg` libraries using `conda`.

If not installed, install them:

```
conda install conda-forge::libopus
conda install conda-forge::ffmpeg
```

### 3. TTS often fails, often times out ⏰

Suggestion: If `EdgeTTS` often fails, first check if you are using a proxy (VPN). If you are, try closing the proxy and try again;  
If you are using Volcano Engine's Doubao TTS and it often fails, it's recommended to use the paid version, as the test version only supports 2 concurrent connections.

### 4. Can connect to self-built server using Wifi, but cannot connect in 4G mode 🔐

Reason: Xia Ge's firmware requires secure connections in 4G mode.

Solution: Currently there are two methods to solve this. Choose one:

1. Modify the code. Refer to this video for solution: https://www.bilibili.com/video/BV18MfTYoE85

2. Use nginx to configure SSL certificate. Refer to tutorial: https://icnt94i5ctj4.feishu.cn/docx/GnYOdMNJOoRCljx1ctecsj9cnRe

### 5. How to improve Xiao Zhi's conversation response speed? ⚡

This project's default configuration is a low-cost solution. It's recommended for beginners to first use the default free models to solve the "can run" problem, then optimize for "run fast".  
If you need to improve response speed, you can try replacing various components. Starting from version `0.5.2`, the project supports streaming configuration. Compared to earlier versions, response speed has improved by about `2.5 seconds`, significantly improving user experience.

| Module Name | Beginner All-Free Setup | Streaming Configuration |
|:---:|:---:|:---:|
| ASR(Speech Recognition) | FunASR(Local) | 👍FunASR(Local GPU Mode) |
| LLM(Large Language Model) | ChatGLMLLM(Zhipu glm-4-flash) | 👍AliLLM(qwen3-235b-a22b-instruct-2507) or 👍DoubaoLLM(doubao-1-5-pro-32k-250115) |
| VLLM(Vision Large Language Model) | ChatGLMVLLM(Zhipu glm-4v-flash) | 👍QwenVLVLLM(Qianwen qwen2.5-vl-3b-instructh) |
| TTS(Text-to-Speech) | ✅LinkeraiTTS(Lingxi Streaming) | 👍HuoshanDoubleStreamTTS(Volcano Double Streaming TTS) or 👍AliyunStreamTTS(Alibaba Cloud Streaming TTS) |
| Intent(Intent Recognition) | function_call(Function Call) | function_call(Function Call) |
| Memory(Memory Function) | mem_local_short(Local Short-term Memory) | mem_local_short(Local Short-term Memory) |

If you care about the time consumption of various components, please check [Xiao Zhi Various Components Performance Test Report](https://github.com/xinnan-tech/xiaozhi-performance-research). You can actually test in your environment according to the test methods in the report.

### 6. I speak slowly, Xiao Zhi keeps interrupting during pauses 🗣️

Suggestion: Find the following part in the configuration file and increase the value of `min_silence_duration_ms` (for example, change to `1000`):

```yaml
VAD:
  SileroVAD:
    threshold: 0.5
    model_dir: models/snakers4_silero-vad
    min_silence_duration_ms: 700  # If speech pauses are longer, you can increase this value
```

### 7. Deployment Related Tutorials
1. [How to perform minimal deployment](./Deployment.md)<br/>
2. [How to perform full module deployment](./Deployment_all.md)<br/>
2. [How to deploy MQTT gateway to enable MQTT+UDP protocol](./mqtt-gateway-integration.md)<br/>
3. [How to automatically pull the latest code of this project and automatically compile and start](./dev-ops-integration.md)<br/>
4. [How to integrate with Nginx](https://github.com/xinnan-tech/xiaozhi-esp32-server/issues/791)<br/>

### 8. Firmware Compilation Related Tutorials
1. [How to compile Xiao Zhi firmware yourself](./firmware-build.md)<br/>
2. [How to modify OTA address based on Xia Ge's compiled firmware](./firmware-setting.md)<br/>

### 8. Extension Related Tutorials
1. [How to enable mobile number registration for control panel](./ali-sms-integration.md)<br/>
2. [How to integrate HomeAssistant for smart home control](./homeassistant-integration.md)<br/>
3. [How to enable vision model for photo recognition](./mcp-vision-integration.md)<br/>
4. [How to deploy MCP endpoint](./mcp-endpoint-enable.md)<br/>
5. [How to connect to MCP endpoint](./mcp-endpoint-integration.md)<br/>
6. [How to enable voiceprint recognition](./voiceprint-integration.md)<br/>
10. [News plugin source configuration guide](./newsnow_plugin_config.md)<br/>

### 9. Voice Cloning, Local Voice Deployment Related Tutorials
1. [How to deploy and integrate index-tts local voice](./index-stream-integration.md)<br/>
2. [How to deploy and integrate fish-speech local voice](./fish-speech-integration.md)<br/>
3. [How to deploy and integrate PaddleSpeech local voice](./paddlespeech-deploy.md)<br/>

### 10. Performance Testing Tutorials
1. [Various component speed testing guide](./performance_tester.md)<br/>
2. [Regular public test results](https://github.com/xinnan-tech/xiaozhi-performance-research)<br/>

### 13. More questions, you can contact us for feedback 💬

You can submit your questions in [issues](https://github.com/xinnan-tech/xiaozhi-esp32-server/issues).
