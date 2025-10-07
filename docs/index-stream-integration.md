# IndexStreamTTS Usage Guide

## Environment Preparation
### 1. Clone Project (Note: Using VLLM1.0 Releases Version)
```bash 
https://github.com/Ksuriuri/index-tts-vllm/releases/tag/IndexTTS-vLLM-1.0
```
Enter the extracted directory:
```bash
cd index-tts-vllm
```

### 2. Create and Activate conda Environment
```bash 
conda create -n index-tts-vllm python=3.12
conda activate index-tts-vllm
```

### 3. Install PyTorch Version 2.8.0 Required (Latest Version)
#### Check the highest version supported by the graphics card and the actually installed version
```bash
nvidia-smi
nvcc --version
``` 
#### Highest CUDA Version Supported by Driver
```bash
CUDA Version: 12.8
```
#### Actually Installed CUDA Compiler Version
```bash
Cuda compilation tools, release 12.8, V12.8.89
```
#### Then the corresponding installation command (pytorch defaults to driver version 12.8)
```bash
pip install torch torchvision
```
Requires pytorch version 2.8.0 (corresponding to vllm 0.10.2). For specific installation instructions, please refer to: [pytorch official website](https://pytorch.org/get-started/locally/)  

### 4. Install Dependencies
```bash 
pip install -r requirements.txt
```

### 5. Download Model Weights
These are official weight files, download to any local path, supports IndexTTS-1.5 weights  
| HuggingFace                                                   | ModelScope                                                          |
|---------------------------------------------------------------|---------------------------------------------------------------------|
| [IndexTTS](https://huggingface.co/IndexTeam/Index-TTS)        | [IndexTTS](https://modelscope.cn/models/IndexTeam/Index-TTS)        |
| [IndexTTS-1.5](https://huggingface.co/IndexTeam/IndexTTS-1.5) | [IndexTTS-1.5](https://modelscope.cn/models/IndexTeam/IndexTTS-1.5) |

Below uses ModelScope installation method as example  
### Please note: git needs to be installed and lfs initialized and enabled (if already installed can skip)
```bash
sudo apt-get install git-lfs
git lfs install
```
Create model directory and pull model:
```bash 
mkdir model_dir
cd model_dir
git clone https://www.modelscope.cn/IndexTeam/IndexTTS-1.5.git
```

### 5. Model Weight Conversion
```bash 
bash convert_hf_format.sh /path/to/your/model_dir
```
For example: If you downloaded the IndexTTS-1.5 model and stored it in the model_dir directory, then execute the following command:
```bash
bash convert_hf_format.sh model_dir/IndexTTS-1.5
```
This operation will convert the official model weights to a transformers library compatible version, saved in the vllm folder under the model weight path, convenient for subsequent vllm library to load model weights.

### 6. Change Interface to Adapt to This Project
The interface return data is not compatible with the project and needs adjustment to directly return audio data:
```bash
vi api_server.py
```
```bash 
@app.post("/tts", responses={
    200: {"content": {"application/octet-stream": {}}},
    500: {"content": {"application/json": {}}}
})
async def tts_api(request: Request):
    try:
        data = await request.json()
        text = data["text"]
        character = data["character"]

        global tts
        sr, wav = await tts.infer_with_ref_audio_embed(character, text)

        return Response(content=wav.tobytes(), media_type="application/octet-stream")
        
    except Exception as ex:
        tb_str = ''.join(traceback.format_exception(type(ex), ex, ex.__traceback__))
        print(tb_str)
        return JSONResponse(
            status_code=500,
            content={
                "status": "error",
                "error": str(tb_str)
            }
        )
```

### 7. Write sh Startup Script (Please note to run in the corresponding conda environment)
```bash 
vi start_api.sh
```
### Paste the following content and press : then enter wq to save  
#### /home/system/index-tts-vllm/model_dir/IndexTTS-1.5 in the script should be modified to the actual path
```bash
# Activate conda environment
conda activate index-tts-vllm 
echo "Activating project conda environment"
sleep 2
# Find process ID occupying port 11996
PID_VLLM=$(sudo netstat -tulnp | grep 11996 | awk '{print $7}' | cut -d'/' -f1)

# Check if process ID was found
if [ -z "$PID_VLLM" ]; then
  echo "No process found occupying port 11996"
else
  echo "Found process occupying port 11996, process ID: $PID_VLLM"
  # First try normal kill, wait 2 seconds
  kill $PID_VLLM
  sleep 2
  # Check if process still exists
  if ps -p $PID_VLLM > /dev/null; then
    echo "Process still running, force terminating..."
    kill -9 $PID_VLLM
  fi
  echo "Process $PID_VLLM has been terminated"
fi

# Find VLLM::EngineCore process
GPU_PIDS=$(ps aux | grep -E "VLLM|EngineCore" | grep -v grep | awk '{print $2}')

# Check if process ID was found
if [ -z "$GPU_PIDS" ]; then
  echo "No VLLM related processes found"
else
  echo "Found VLLM related processes, process IDs: $GPU_PIDS"
  # First try normal kill, wait 2 seconds
  kill $GPU_PIDS
  sleep 2
  # Check if process still exists
  if ps -p $GPU_PIDS > /dev/null; then
    echo "Process still running, force terminating..."
    kill -9 $GPU_PIDS
  fi
  echo "Process $GPU_PIDS has been terminated"
fi

# Create tmp directory (if doesn't exist)
mkdir -p tmp

# Run api_server.py in background, log redirected to tmp/server.log
nohup python api_server.py --model_dir /home/system/index-tts-vllm/model_dir/IndexTTS-1.5 --port 11996 > tmp/server.log 2>&1 &
echo "api_server.py is running in background, logs please check tmp/server.log"
```
Give script execution permission and run script:
```bash 
chmod +x start_api.sh
./start_api.sh
```
Logs will be output in tmp/server.log, can view log situation through the following command:
```bash
tail -f tmp/server.log
```

## Voice Configuration
index-tts-vllm supports registering custom voices through configuration files, supports single voice and mixed voice configuration.  
Configure custom voices in the assets/speaker.json file in the project root directory.

### Configuration Format Description
```bash
{
    "Speaker Name 1": [
        "Audio file path 1.wav",
        "Audio file path 2.wav"
    ],
    "Speaker Name 2": [
        "Audio file path 3.wav"
    ]
}
```

### Note (Need to restart service for registration)
After adding, need to add corresponding speakers in the control panel (for single module, change the corresponding voice).
