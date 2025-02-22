# 🌔 moondream

a tiny vision language model that kicks ass and runs anywhere

[Website](https://moondream.ai/) | [Hugging Face](https://huggingface.co/vikhyatk/moondream2) | [Demo](https://huggingface.co/spaces/vikhyatk/moondream2)

## Benchmarks

moondream2 is a 1.86B parameter model initialized with weights from [SigLIP](https://huggingface.co/timm/ViT-SO400M-14-SigLIP-384) and [Phi 1.5](https://huggingface.co/microsoft/phi-1_5).

| Model | VQAv2 | GQA | TextVQA | POPE | TallyQA |
| --- | --- | --- | --- | --- | --- |
| moondream1 | 74.7 | 57.9 | 35.6 | - | - |
| **moondream2** (latest) | 75.4 | 59.8 | 43.1 | (coming soon) | (coming soon) |

## Examples

| Image | Example |
| --- | --- |
| ![](assets/demo-1.jpg) | **What is the girl doing?**<br>The girl is eating a hamburger.<br><br>**What color is the girl's hair?**<br>White |
| ![](assets/demo-2.jpg) | **What is this?**<br>A rack is present in the image, containing various electronic devices. A chair is situated on the left side, and a brick wall is visible in the background.<br><br>**What is behind the stand?**<br>A brick wall is visible behind the stand. |

## Usage

**Using transformers** (recommended)

```bash
pip install transformers timm einops
```

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
from PIL import Image

model_id = "vikhyatk/moondream2"
revision = "2024-03-06"
model = AutoModelForCausalLM.from_pretrained(
    model_id, trust_remote_code=True, revision=revision
)
tokenizer = AutoTokenizer.from_pretrained(model_id, revision=revision)

image = Image.open('<IMAGE_PATH>')
enc_image = model.encode_image(image)
print(model.answer_question(enc_image, "Describe this image.", tokenizer))
```

The model is updated regularly, so we recommend pinning the model version to a
specific release as shown above.

To enable Flash Attention on the text model, pass in `attn_implementation="flash_attention_2"`
when instantiating the model.

```python
model = AutoModelForCausalLM.from_pretrained(
    model_id, trust_remote_code=True, revision=revision,
    torch_dtype=torch.float16, attn_implementation="flash_attention_2"
).to("cuda")
```

Batch inference is also supported.

```python
answers = moondream.batch_answer(
    images=[Image.open('<IMAGE_PATH_1>'), Image.open('<IMAGE_PATH_2>')],
    prompts=["Describe this image.", "Are there people in this image?"],
    tokenizer=tokenizer,
)
```

**Using this repository**

Clone this repository and install dependencies.

```bash
pip install -r requirements.txt
```

`sample.py` provides a CLI interface for running the model. When the `--prompt` argument is not provided, the script will allow you to ask questions interactively.

```bash
python sample.py --image [IMAGE_PATH] --prompt [PROMPT]
```

Use `gradio_demo.py` script to start a Gradio interface for the model.

```bash
python gradio_demo.py
```

`webcam_gradio_demo.py` provides a Gradio interface for the model that uses your webcam as input and performs inference in real-time.

```bash
python webcam_gradio_demo.py
```

**Limitations**

* The model may generate inaccurate statements, and struggle to understand intricate or nuanced instructions.
* The model may not be free from societal biases. Users should be aware of this and exercise caution and critical thinking when using the model.
* The model may generate offensive, inappropriate, or hurtful content if it is prompted to do so.
