## SAMI: Self-Supervised Alignment with Mutual Information

### 🧐 What this is

This repository contains a reference implementation of SAMI (Self-Supervised Alignment with Mutual Information) using the [TL;DR](https://huggingface.co/datasets/openai/summarize_from_feedback) dataset. 

### 🚀 Running the Code

#### Prerequisites

- Set up a conda environment (we used `python==3.10.0`) and install the required dependencies by running `pip install -e .`

#### Data Generation (Optional)

1. Adjust the `experiments/tldr/config/generate.yaml` config file to match your directories and desired configurations. 
2. Navigate to `cd experiments/tldr` and run `python generate.py` to generate your own data. By default, the generated data will be stored in `experiments/tldr/data/base`. Note that this directory is already populated with the data used in the paper if you prefer to finetune a model directly.

#### Training

1. Select a model configuration (e.g., `mistral-7b`) from the `experiments/tldr/conf/model` directory and update the `cache_dir` accordingly (e.g., `/scr/YOUR_USERNAME/sami/checkpoints`).
2. Adjust the `experiments/tldr/conf/train_sami.yaml` config as needed, including optional [wandb](https://wandb.ai) logging. If you set `log: true` you should have an account/make sure that you are logged in.
3. Navigate to `cd experiments/tldr` and run training using an interactive job using the command below, or adapt the example slurm script to meet your computing needs and submit it using `sbatch` (or modify the script to be a standard bash script and submit from e.g. a `tmux` window).

```bash
python train.py \
    training.beta=0.0 \
    wandb.name="$YOUR_WANDB_NAME" \
    training.checkpoint_dir="$YOUR_CHECKPOINT_DIR" \
    training.lr=5e-7 \
    data_path="data/base" \
    data_file="base_mistral_from_mistral_principles.json" \
    n_examples=2000
```

#### Evaluation

1. Adjust the `experiments/tldr/config/evaluate.yaml` configuration, navigate to `cd experiments/tldr` and run `python evaluate.py`. This will write the generated responses into `experiments/tldr/results/responses`.
2. Compute win rates by adjusting the `experiments/tldr/config/win_rates.yaml` configuration and running `python win_rates.py` from the same directory. Note that this script currently uses azure, so if you dont have access to GPT-4 via azure, you might have to copy-paste the `/scr/models/openai_models/azure.py` and create your own `AsyncOpenAI` class. FYI: We used the `gpt-4-0613` snapshot for all evaluations. 

#### Running without GPUs

If you don't have access to GPUs, you can attempt to run training using `experiments/tldr/conf/model/mistral_tiny_base`, which we tested locally on an Apple M2 Pro (2023 MacBook Pro with 16B memory).
