Getting Started with MLC-LLM and Llama 2 in Google Colab
--------------------------------------------------------

This guide will help you set up and use the MLC-LLM ChatModule to interact with the Llama 2 model. We will be working within a Google Colab notebook for the easiest setup.

### Environment Setup

1.  **Conda Environment (optional for non-Colab users)**:

    -   If you are using Conda (not required for Colab), create and activate the environment:

```
        # bash
        conda create --name mlc-llm python=3.10
        conda activate mlc-llm
```

2.  **GPU Setup for Colab**:

    -   If using Google Colab, make sure to enable GPU:
        -   Go to *Runtime > Change runtime type* and select "GPU" as the hardware accelerator.
        -   Click "Connect" in the top-right corner to start a GPU session.
3.  **Check CUDA Setup (optional)**:

    -   To verify if CUDA is correctly set up, run:

```
        # bash
        !nvidia-smi
```

4.  **Install MLC-AI and MLC-LLM Packages**:

    -   Download and install the required nightly build packages. Replace the hardware-specific command with the appropriate one from [MLC AI](https://mlc.ai/package/):

```
        # bash
        !pip install --pre --force-reinstall mlc-ai-nightly-cu118 mlc-llm-nightly-cu118 -f https://mlc.ai/wheels
```

### Download the Model Weights and Libraries

1.  **Install Git LFS (required for large files)**:

    -   First, install `git-lfs` if needed (not required in Colab):

```
        # bash
        !git lfs install
```

2.  **Download Prebuilt Libraries and Llama 2 Model**:

    -   These commands will download the necessary files, including prebuilt libraries and Llama-2-7b configuration. This may take time:

```
        # bash
        !mkdir -p dist
        !git clone https://github.com/mlc-ai/binary-mlc-llm-libs.git dist/prebuilt_libs
        !cd dist && git clone https://huggingface.co/mlc-ai/Llama-2-7b-chat-hf-q4f16_1-MLC
```

3.  **Restart Runtime**:

    -   After downloading, you'll need to restart the runtime because the notebook won't automatically detect the newly installed module:

```
        # bash
        exit()
```

### Using the ChatModule

1.  **Import the Necessary Libraries**:

    -   After restarting, import the libraries and set up the *ChatModule*:

```
        # python
        from mlc_llm import ChatModule
        from mlc_llm.callback import StreamToStdout

        cm = ChatModule(
            model="dist/Llama-2-7b-chat-hf-q4f16_1-MLC",
            model_lib_path="dist/prebuilt_libs/Llama-2-7b-chat-hf/Llama-2-7b-chat-hf-q4f16_1-cuda.so"
        )
```
    -   For other platforms (e.g., Linux Vulkan, macOS Metal), update 'model_lib_path' accordingly:

        -   Vulkan: Llama-2-7b-chat-hf-q4f16_1-vulkan.so
        -   Metal: Llama-2-7b-chat-hf-q4f16_1-metal.so
        
2.  **Chat with the Model**:

    -   To generate responses from the model, you can pass prompts like this:

```
        # python
        output = cm.generate(
            prompt="When was Python released?",
            progress_callback=StreamToStdout(callback_interval=2),
        )
```


    -   For chat-style interactions, use:


```
        # python
        prompt = input("Prompt: ")
        output = cm.generate(prompt=prompt, progress_callback=StreamToStdout(callback_interval=2))
```

3.  **Check the Chatbot's Generation Speed**:

    -   To check performance statistics:

```
        #python
        print(cm.stats())
```

4.  **Reset the Chat History**:

    -   If you want to reset the model's chat history:

```
        # python
        cm.reset_chat()
```

### Benchmarking the Model

1.  **Benchmarking**:
    -   To benchmark the model's performance, you can use `benchmark_generate`. This method measures how fast the model generates tokens for a given prompt:

```
        # python
        print(cm.benchmark_generate(prompt="What is benchmark?", generate_length=512))
        cm.stats()
```
