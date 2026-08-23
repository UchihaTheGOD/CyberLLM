[README.md](https://github.com/user-attachments/files/31348455/README.md)
# SentinelSmolLM2-360M-V8

A small cybersecurity-focused language model based on **SmolLM2-360M-Instruct**.

## Model Files

This repository contains the trained/fine-tuned Sentinel model:

- `model.safetensors` — model weights
- `config.json` — model configuration
- `generation_config.json` — generation settings
- `tokenizer.json` — tokenizer
- `tokenizer_config.json` — tokenizer configuration
- `chat_template.jinja` — chat template
- `training_args.bin` — training arguments

## Requirements

- Python 3.10+
- PyTorch
- Transformers
- Accelerate
- CUDA GPU recommended for faster inference

Install the dependencies:

```bash
pip install torch transformers accelerate
```

For a CUDA installation of PyTorch, use the command recommended for your CUDA version from the official PyTorch website.

## Run the Model

Put this README and all model files in the same directory.

Example:

```text
SentinelSmolLM2-360M-V8/
├── chat_template.jinja
├── config.json
├── generation_config.json
├── model.safetensors
├── tokenizer.json
├── tokenizer_config.json
├── training_args.bin
└── README.md
```

Create a file named `run.py`:

```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

MODEL_PATH = "./SentinelSmolLM2-360M-V8"

device = "cuda" if torch.cuda.is_available() else "cpu"

tokenizer = AutoTokenizer.from_pretrained(
    MODEL_PATH,
    local_files_only=True
)

model = AutoModelForCausalLM.from_pretrained(
    MODEL_PATH,
    torch_dtype=torch.float16 if device == "cuda" else torch.float32,
    local_files_only=True
).to(device)

prompt = "Explain what a firewall does in cybersecurity."

inputs = tokenizer(prompt, return_tensors="pt").to(device)

with torch.no_grad():
    outputs = model.generate(
        **inputs,
        max_new_tokens=200,
        temperature=0.7,
        do_sample=True,
        top_p=0.9
    )

response = tokenizer.decode(outputs[0], skip_special_tokens=True)

print(response)
```

Run:

```bash
python run.py
```

## Using the Chat Template

For chat-style inference, use the tokenizer's built-in chat template:

```python
messages = [
    {"role": "user", "content": "What is SQL injection?"}
]

inputs = tokenizer.apply_chat_template(
    messages,
    return_tensors="pt",
    add_generation_prompt=True
).to(device)

with torch.no_grad():
    outputs = model.generate(
        inputs,
        max_new_tokens=200,
        temperature=0.7,
        do_sample=True
    )

print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

## Hardware

The model is designed to be relatively lightweight compared with larger language models.

A CUDA GPU is recommended for faster inference. CPU inference is also possible but will be slower.

## Intended Use

SentinelSmolLM2-360M-V8 is intended for:

- Cybersecurity education
- Security-related question answering
- Security concept explanations
- Experimentation with small language models
- Research and development

## Limitations

This is a small experimental cybersecurity model. It may produce incorrect, incomplete, or unsafe information.

Always verify security-related output before using it in a real environment.

The model should not be treated as a replacement for professional security tools, testing, or human review.

## Version

**SentinelSmolLM2-360M-V8**

Base model: **SmolLM2-360M-Instruct**

## License

Add the applicable license here before publishing the model.

If this model is derived from another model, make sure the original model's license and attribution requirements are followed.
