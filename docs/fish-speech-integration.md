# Log in to AutoDL, Rent Image
Select image:
```
PyTorch / 2.1.0 / 3.10(ubuntu22.04) / cuda 12.1
```

After machine starts, set academic acceleration:
```
source /etc/network_turbo
```

Enter working directory:
```
cd autodl-tmp/
```

Pull project:
```
git clone https://gitclone.com/github.com/fishaudio/fish-speech.git ; cd fish-speech
```

Install dependencies:
```
pip install -e.
```

If error occurs, install portaudio:
```
apt-get install portaudio19-dev -y
```

After installation, execute:
```
pip install torch==2.3.1 torchvision==0.18.1 torchaudio==2.3.1 --index-url https://download.pytorch.org/whl/cu121
```

Download model:
```
cd tools
python download_models.py 
```

After downloading model, run interface:
```
python -m tools.api_server --listen 0.0.0.0:6006 
```

Then go to the autodl instance page in browser:
```
https://autodl.com/console/instance/list
```

As shown below, click the `Custom Service` button of your machine to enable port forwarding service:
![Custom Service](images/fishspeech/autodl-01.png)

After port forwarding service setup is completed, open the URL `http://localhost:6006/` on your local computer to access the fish-speech interface:
![Service Preview](images/fishspeech/autodl-02.png)

If you are using single module deployment, the core configuration is as follows:
```
selected_module:
  TTS: FishSpeech
TTS:
  FishSpeech:
    reference_audio: ["config/assets/wakeup_words.wav",]
    reference_text: ["Hello, I'm Xiao Zhi, a Taiwanese girl with a nice voice, super happy to meet you, what have you been up to lately, don't forget to give me some interesting stuff, I love hearing gossip",]
    api_key: "123"
    api_url: "http://127.0.0.1:6006/v1/tts"
```

Then restart the service.
