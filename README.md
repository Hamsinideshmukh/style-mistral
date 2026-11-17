# Style-Mistral ✧

**QLoRA fine-tuning of Mistral-7B-Instruct for personalized outfit recommendation.**

Give it a body type, a personal style and an event. It returns five complete outfits, each broken down into top, bottom, shoes and accessories.


---

## Results

Measured against the base model on held-out examples the model never saw during training.

| Metric | Base | Fine-tuned |
|---|---|---|
| Held-out loss (150 examples) | 0.939 | **0.608** (−35%) |
| Perplexity | 2.56 | **1.84** |
| ROUGE-L (10 examples) | 0.262 | **0.339** (+29%) |
| Uses the target outfit format | mixed (paragraphs / lists) | **10 / 10** |

| Training | |
|---|---|
| Trainable parameters | 41.9M / 7.29B (0.58%) |
| Training examples | 1,500 |
| Train loss | 0.853 → 0.613 |
| Peak GPU memory | 7.6 GB (Tesla T4) |

---

## Method

| | |
|---|---|
| Base model | `mistralai/Mistral-7B-Instruct-v0.3` |
| Quantization | 4-bit NF4 + double quantization (bitsandbytes) |
| Adapters | LoRA, r = 16, α = 32, on all attention and MLP projections |
| Optimizer | Paged AdamW (32-bit), LR 2e-4, grad-norm clip 0.3 |
| Memory | Gradient checkpointing, effective batch size 16 |
| Loss | Completion-only (prompt tokens masked) |
| Data | [`neuralwork/fashion-style-instruct`](https://huggingface.co/datasets/neuralwork/fashion-style-instruct): 1,500 train / 150 held-out |

This follows the QLoRA recipe (Dettmers et al., 2023): a frozen 4-bit base model, with low-rank adapters trained on every linear layer.

---

## Examples

**Input:** *I'm a tall, slender guy with long limbs who loves modern minimalist styles. I need help finding slim-cut shirts and pants that fit my lanky frame.*  
**Context:** *I'm going to a bachelorette / bachelor party.*

<table>
<tr><th>Base model</th><th>Fine-tuned</th></tr>
<tr><td valign="top">

1\. Modern Minimalist Tuxedo Look:
- Slim-fit black tuxedo jacket with a slim lapel for a sleek silhouette.
- Matching slim-fit black trousers with a matte finish.
- A white slim-fit dress shirt with French cuffs, paired with black cufflinks.
- Black patent leather Oxford shoes and a black bow tie to complete the formal look. …

</td><td valign="top">

**Outfit Combination 1:**
- **Top:** A fitted black turtleneck sweater with a slim collar. This will add sophistication to your look while still maintaining a modern minimalist vibe.
- **Bottom:** Dark wash slim-fit jeans. Opt for a straight or slightly tapered leg to balance out your proportions.
- **Shoes:** Black leather Chelsea boots. These will give you an edgy yet polished touch.
- **Accessories:** A silver watch and a black leather belt with a sleek buckle.

…

</td></tr>
</table>

**Input:** *I'm a 171cm tall athletic woman with slightly broad shoulders. I have a clean / sleak semi-feminine style.*  
**Context:** *I'm going to a semi-casual event.*

<table>
<tr><th>Base model</th><th>Fine-tuned</th></tr>
<tr><td valign="top">

1\. Outfit 1:
- Top: A slim-fitting, high-neck, long-sleeved blouse in a neutral color like beige or navy blue. This will accentuate your clean and sleek style while providing coverage for broader shoulders.
- Bottom: Pair it with a midi skirt in a trendy fabric like leather or silk for a touch of elegance. Choose a skirt with a slight flare at the bottom to balance out your broad shoulders. …

</td><td valign="top">

**Outfit Combination 1:**
- **Top:** A fitted white button-down blouse with a subtle ruffle detail on the collar. This will add a touch of femininity while still maintaining a sleek look.
- **Bottom:** High-waisted black skinny jeans. The high waist will elongate your legs and create a flattering silhouette.
- **Shoes:** Black pointed-toe ankle boots with a low heel. These will add height without sacrificing comfort.
- **Accessories:** A delicate gold pendant necklace and a structured black handbag.

…

</td></tr>
</table>

*Outputs are verbatim, trimmed to the first outfit. All ten before/after examples are in [`results/results.json`](results/results.json).*

---

## Run it

1. Accept the terms on the [Mistral-7B-Instruct-v0.3](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.3) page and add your Hugging Face token as the `HF_TOKEN` secret.
2. Open `QLoRA_mistral_7b.ipynb` on **Kaggle** (GPU T4, Internet on) or **Colab** (T4).
3. Run all. The notebook loads the model, records the baseline, trains, evaluates and saves, all in one pass.

---


## Credits
Dataset from [neuralwork](https://github.com/neuralwork/instruct-finetune-mistral). Method from [QLoRA (Dettmers et al., 2023)](https://arxiv.org/abs/2305.14314).
