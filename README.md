# Sarcasm detection and tone transformation

This team project classifies headlines as sarcastic or non-sarcastic and rewrites them in the opposite tone while trying to keep the meaning the same. We used Qwen2.5-3B-Instruct with LoRA fine-tuning.

My part was the evaluation pipeline and results analysis. This repository explains that work. The implementation is in a private project repository, so there is no runnable training or evaluation code here.

## My contribution

I evaluated two different questions: whether the classifier recognised sarcasm, and whether the generated headline changed tone without losing its meaning.

For classification, I used accuracy, precision, recall, F1 and a confusion matrix. For generation, I combined several checks:

- **BERTScore and cosine similarity:** compare meaning with the reference rewrite.
- **BLEU and ROUGE:** measure overlap with the reference wording.
- **Round-trip classification:** pass the generated headline through the classifier to check the requested tone.
- **Source similarity and edit distance:** flag unchanged or near-copied inputs.
- **Direction-specific analysis:** compare sarcasm removal with sarcasm generation separately.

I implemented these checks, analysed the outputs and wrote the evaluation documentation. The model training, augmentation and compute setup were part of the wider team project.

## Results

| Measure | Reported result |
| --- | ---: |
| Sarcasm classification accuracy | 93.83% |
| Classification F1 | About 0.94 |
| Tone transformation BERTScore F1 | About 0.90 |

The BERTScore result measures similarity to reference rewrites. It is not a 90% success rate for changing tone. The public repository does not include the raw predictions, evaluation scripts or evaluation sample count, so these results cannot be reproduced from this case study alone.

## Why I used several checks

A rewrite can preserve meaning but fail to change sarcasm. It can also get a high similarity score by copying the original headline. That is why I compared outputs with both the reference rewrite and the source, then checked the predicted tone.

The round-trip check is useful, but it still depends on the classifier's own accuracy. It is not a substitute for human evaluation.

The results also differed by direction. Sarcasm removal generally did better on lexical measures than sarcasm generation. Looking at the directions separately helped expose this instead of hiding it in one average score.

Lower BLEU alongside a higher BERTScore was another useful finding: a valid rewrite may use different words from the reference. I interpreted lexical overlap together with the semantic and tone checks rather than treating one score as the final answer.

## Project workflow

The team prepared and balanced the data, added augmented examples, built the classification and generation tasks, fine-tuned the model, and evaluated its predictions.

![Project architecture and evaluation overview](https://raw.githubusercontent.com/Aryann-Sharma/sarcasm-nlp-portfolio/main/project-evaluation-overview.png)

The project used Python and Jupyter notebooks, Qwen2.5-3B-Instruct with LoRA, the OpenAI Batch API for augmentation, and a Slurm-based compute cluster. My main work was the Python evaluation and analysis described above.
