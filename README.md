# WSRL: Warm-Start Reinforcement Learning
[](https:/zhouzypaul.github.io/images/paper-images/wsrl/wsrl.png)
[![arXiv](https://img.shields.io/badge/arXiv-2412.07762-df2a2a.svg)](http://arxiv.org/abs/2412.07762)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Static Badge](https://img.shields.io/badge/Project-Page-a)](https://zhouzypaul.github.io/wsrl)

This is the code release for paper [Efficient Online Reinforcement Learning Fine-Tuning Need Not Retain Offline Data](http://arxiv.org/abs/2412.07762). We provide the implementation of WSRL (Warm-Start Reinforcement Learning), as well as popular RL algorithms in JAX and Flax: IQL, CQL, CalQL, SAC, RLPD.
We support the following environments: D4RL antmaze, adroit, kitchen, and Mujoco locomotion, but the code can be easily adpated to work with other environments and datasets.

```
@article{zhou2024efficient,
  author       = {Zhiyuan Zhou and Andy Peng and Qiyang Li and Sergey Levine and Aviral Kumar},
  title        = {Efficient Online Reinforcement Learning Fine-Tuning Need Not Retain Offline Data},
  conference   = {arXiv Pre-print},
  year         = {2024},
  url          = {http://arxiv.org/abs/2412.07762},
}
```


## Installation
```bash
conda create -n wsrl python=3.10 -y
conda activate wsrl
pip install -r requirements.txt
```

For jax, install
```
pip install --upgrade "jax[cuda11_pip]==0.4.20" -f https://storage.googleapis.com/jax-releases/jax_cuda_releases.html
```

To use the D4RL envs, you would also need my fork of the d4rl envs here:
```
git clone git@github.com:zhouzypaul/D4RL.git
cd D4RL
pip install -e .
```

To use Mujoco, you would also need to install mujoco manually to `~/.mujoco/`, and use the following environment variables
```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/.mujoco/mujoco210/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib/nvidia
```

To use the adroit envs, you would need
```
git clone --recursive https://github.com/nakamotoo/mj_envs.git
cd mj_envs
git submodule update --remote
pip install -e .
```

Download the adroit dataset from [here](https://drive.google.com/file/d/1yUdJnGgYit94X_AvV6JJP5Y3Lx2JF30Y/view) and unzip the files into `~/adroit_data/`.
If you would like to put the adroit datasets into another directory, use the environment variable
```bash
export DATA_DIR_PREFIX=/path/to/your/data
```

## Running
The main run script is `finetune.py`. We provide bash scripts in `experiments/scripts/<ENV>` to train WSRL/IQL/CQL/CalQ/RLPD on the different environments.

The shared agent configs are in `experiments/configs/*`, and the environment-specific configs are in `experiments/configs/train_config.py` and in the bash scripts.

### Pre-training
For example, to run CalQL (with Q-ensemble) pre-training
```bash
# on antmaze
bash experiments/scripts/antmaze/launch_calql_finetune.sh --use_redq --env antmaze-large-diverse-v2

# on adroit
bash experiments/scripts/adroit/launch_calql_finetune.sh --use_redq --env door-binary-v0

# on kitchen
bash experiments/scripts/kitchen/launch_calql_finetune.sh --use_redq --env kitchen-mixed-v0

# on mujoco locomotion
bash experiments/scripts/locomotion/launch_calql_finetune.sh --use_redq --env halfcheetah-medium-replay-v0
```

### Fine-tuning
To run WSRL fine-tuning from a pre-trained checkpoint
```bash
# on antmaze
bash experiments/scripts/antmaze/launch_wsrl_finetune.sh --env antmaze-large-diverse-v2 --resume_path /path/to/checkpoint

# on adroit
bash experiments/scripts/adroit/launch_wsrl_finetune.sh --env door-binary-v0 --resume_path /path/to/checkpoint

# on kitchen
bash experiments/scripts/kitchen/launch_wsrl_finetune.sh --env kitchen-mixed-v0 --resume_path /path/to/checkpoint

# on mujoco locomotion
bash experiments/scripts/locomotion/launch_wsrl_finetune.sh --env halfcheetah-medium-replay-v0 --resume_path /path/to/checkpoint
```

### No Data Retention
The default setting is to not retain offline data during fine-tuning, as described in the [paper](http://arxiv.org/abs/2412.07762). However, if you wish to retain the data, you can use the `--mixing_ratio <>` or `--online_sampling_method append` option. Checkout `finetune.py` for more details.

## Contributing
To enable code checks and auto-formatting, please install pre-commit hooks (run this in the root directory):
```
pre-commit install
```
The hooks should now run before every commit. If files are modified during the checks, you'll need to re-stage them and commit again.

## Credits
This repo is built upon a version of Dibya Ghosh's [jaxrl_minimal](https://github.com/dibyaghosh/jaxrl_minimal) repository with contributions also from Kevin Black, Homer Walke, Kyle Stachowicz, and others.