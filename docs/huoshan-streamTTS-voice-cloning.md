# Volcano Bidirectional Streaming TTS + Voice Cloning Configuration Tutorial
Under single module deployment, use Volcano Engine bidirectional streaming speech synthesis service while performing voice cloning, supporting WebSocket protocol streaming calls.

### 1. Activate Volcano Engine Service
Visit https://console.volcengine.com/speech/app to create an application in application management, check Speech Synthesis Large Model and Voice Replication Large Model. Click Voice Replication Large Model in the left list, then scroll down to obtain App Id, Access Token, Cluster ID, and Voice ID (S_xxxxx)

### 2. Clone Voice
For voice cloning, please refer to the tutorial: https://github.com/104gogo/huoshan-voice-copy

Prepare an audio file of 10-30 seconds (.wav format) and add it to the cloning project. Fill the keys obtained from the platform into ```uploadAndStatus.py``` and ```tts_http_demo.py```

In uploadAndStatus.py, modify audio_path= to your own .wav file name:
```python
train(appid=appid, token=token, audio_path=r".\audios\xiaohe.wav", spk_id=spk_id)
```

Run the following commands to generate test_submit.mp3, click play to listen to the cloning effect:

```python
python uploadAndStatus.py
python tts_http_demo.py
```
Return to the Volcano Engine console page, refresh to see that the voice replication details status is replication successful.

### 3. Fill Configuration File
Fill the keys obtained from the Volcano Engine service application into the HuoshanDoubleStreamTTS configuration in .config.yaml

Modify the resource_id parameter to ``` volc.megatts.default``` 
(Reference official documentation: https://www.volcengine.com/docs/6561/1329505)
Fill the speaker parameter with the Voice ID (S_xxxxx)

Start the service, if the voice emitted when waking up Xiao Zhi is the cloned voice, it means success.
