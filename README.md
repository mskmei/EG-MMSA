

# EG-MMSA

EG-MMSA is a modified version of MMSA by THUIAR. EG-MMSA integrate the module of emotion-guided for various MSA structures like MISA ans MMIM, which will pre-extract prior emotion information from raw audio data and apply weighted sum to guide the fusion procedure. In this work, Wav2Vec model is used to extract emotion feature, and an MLP is equipped to transfrom the emtion feature to a score matrix, which is soon be used in the process of fusion to achieve the feature alignment of different modalities. The method to integrate the emtion-guided module is different due to different MSA structures, considering their different mathematical principle in the fusion procedure and their different transformation of feature vectors. We now apply the idea of emtion-guided module on 2 famous traditional frameworks based on MMSA, MISA and MMIM. More models are expected to be modified in the near future and some bias purification method may also be explored.

According to our recent results, the emtion-guided strategy can improce the performance of MSA models on CMU-MOSI dataset on most of the test metrics, and the performance of EG-Models exceeds most of baseline models we selected in our report. Further experiment will be carried out on larger dataset like CMU-MOSEI in the near future. The preliminary results for EG-MISA and EG-MMIM compared to baseline models are shown in [result file](EG-results/results.md).

## Model Architecture of EG-Model

As we described above, we modify the network structure of MSA models and add an emtion-guided module. The below images give example of emtion-guided module in the framework of MISA and MMIM. The improved frameworks are called EG-MISA and EG-MMIM repectively.

### EG-MISA

![Structure of EG-MISA](images/EG-MISA.jpg)

### EG-MMIM

![Structure of EG-MMIM](images/EG-MMIM.jpg)



## Features

- **Now, EG-MMSA only supports MISA and MMIM on CMU-MOSI Dataset**
- Train, test and compare multiple MSA models in a unified framework.
- Supports [15](#3-supported-msa-models) MSA models, including recent works.
- Supports 3 MSA datasets: [MOSI](https://ieeexplore.ieee.org/abstract/document/7742221), [MOSEI](https://aclanthology.org/P18-1208.pdf), and [CH-SIMS](https://aclanthology.org/2020.acl-main.343/).
- Easy to use, provides Python APIs and commandline tools.
- Experiment with fully customized multimodal features extracted by [MMSA-FET](https://github.com/thuiar/MMSA-FET) toolkit.

## 1. Get Started

> **Note:** From version 2.0, we packaged the project and uploaded it to PyPI in the hope of making it easier to use. If you don't like the new structure, you can always switch back to `v_1.0` branch. 

### 1.1 Use Python API

- Run `pip install MMSA` in your python virtual environment.
- Import and use in any python file:

  ```python
  from MMSA import MMSA_run

  # run LMF on MOSI with default hyper parameters
  MMSA_run('lmf', 'mosi', seeds=[1111, 1112, 1113], gpu_ids=[0])

  # tune Self_mm on MOSEI with default hyper parameter range
  MMSA_run('self_mm', 'mosei', seeds=[1111], gpu_ids=[1])

  # run TFN on SIMS with altered config
  config = get_config_regression('tfn', 'mosi')
  config['post_fusion_dim'] = 32
  config['featurePath'] = '~/feature.pkl'
  MMSA_run('tfn', 'mosi', config=config, seeds=[1111])

  # run MTFN on SIMS with custom config file
  MMSA_run('mtfn', 'sims', config_file='./config.json')
  ```

  If you want to reproduce EG-MMSA, use MISA and MMIM as models since they are already modified and use [config_regression](src/MMSA/config/config_regression.json) as config file to training the model on CMU-MOSI dataset.

- For more detailed usage, please refer to [APIs](https://github.com/thuiar/MMSA/wiki/APIs).

### 1.2 Use Commandline Tool

- Run `pip install MMSA` in your python virtual environment.
- Use from command line:

  ```bash
  # show usage
  $ python -m MMSA -h

  # train & test LMF on MOSI with default parameters
  $ python -m MMSA -d mosi -m lmf -s 1111 -s 1112

  # tune 50 times of TFN on MOSEI with custom config file & custom save dir
  $ python -m MMSA -d mosei -m tfn -t -tt 30 --model-save-dir ./models --res-save-dir ./results

  # train & test self_mm on SIMS with custom audio features & use gpu2
  $ python -m MMSA -d sims -m self_mm -Fa ./Features/Feature-A.pkl --gpu-ids 2
  ```

If you want to reproduce EG-MMSA, use MISA and MMIM as models since they are already modified and use [config_regression](src/MMSA/config/config_regression.json) as config file to training the model on CMU-MOSI dataset.
An Example of use should be:

```bash
$ python -m MMSA -d mosi -m misa -s 1111 -s 1112 -r MMSA/config/config_regression.json
$ python -m MMSA -d mosi -m mmim -s 1111 -s 1112 -r MMSA/config/config_regression.json
```

- For more detailed usage, please refer to [Commandline Arguments](https://github.com/thuiar/MMSA/wiki/Commandline-Arguments).


## 2. Datasets

EG-MMSA only supports CMU-MOSI right now. MMSA currently supports MOSI, MOSEI, and CH-SIMS dataset. Use the following links to download raw videos, feature files and label files. You don't need to download raw videos if you're not planning to run end-to-end tasks. 

- [BaiduYun Disk](https://pan.baidu.com/s/1XmobKHUqnXciAm7hfnj2gg) `code: mfet`
- [Google Drive](https://drive.google.com/drive/folders/1A2S4pqCHryGmiqnNSPLv7rEg63WvjCSk?usp=sharing)

SHA-256 for feature files:

```text
`MOSI/Processed/unaligned_50.pkl`:  `78e0f8b5ef8ff71558e7307848fc1fa929ecb078203f565ab22b9daab2e02524`
`MOSI/Processed/aligned_50.pkl`:    `d3994fd25681f9c7ad6e9c6596a6fe9b4beb85ff7d478ba978b124139002e5f9`
`MOSEI/Processed/unaligned_50.pkl`: `ad8b23d50557045e7d47959ce6c5b955d8d983f2979c7d9b7b9226f6dd6fec1f`
`MOSEI/Processed/aligned_50.pkl`:   `45eccfb748a87c80ecab9bfac29582e7b1466bf6605ff29d3b338a75120bf791`
`SIMS/Processed/unaligned_39.pkl`:  `c9e20c13ec0454d98bb9c1e520e490c75146bfa2dfeeea78d84de047dbdd442f`
```

MMSA uses feature files that are organized as follows:

```python
{
    "train": {
        "raw_text": [],              # raw text
        "audio": [],                 # audio feature
        "vision": [],                # video feature
        "id": [],                    # [video_id$_$clip_id, ..., ...]
        "text": [],                  # bert feature
        "text_bert": [],             # word ids for bert
        "audio_lengths": [],         # audio feature lenth(over time) for every sample
        "vision_lengths": [],        # same as audio_lengths
        "annotations": [],           # strings
        "classification_labels": [], # Negative(0), Neutral(1), Positive(2). Deprecated in v_2.0
        "regression_labels": []      # Negative(<0), Neutral(0), Positive(>0)
    },
    "valid": {***},                  # same as "train"
    "test": {***},                   # same as "train"
}
```

> **Note:** For MOSI and MOSEI, the pre-extracted text features are from BERT, different from the original glove features in the [CMU-Multimodal-SDK](http://immortal.multicomp.cs.cmu.edu/raw_datasets/processed_data/).

> **Note:** If you wish to extract customized multimodal features, please try out our [MMSA-FET](https://github.com/thuiar/MMSA-FET)

## 3. Suported EG-MSA Models
|    Type     |                   Model Name                            |                                          From                                          |    Published      |
| :---------: | :-----------------------------------------------------: | :------------------------------------------------------------------------------------: | :---------------: |
| Single-Task |       [MISA](src/MMSA/models/singleTask/MISA.py)        |                      [MISA](https://github.com/declare-lab/MISA)                       | ACMMM 2020        |
| Single-Task |       [MMIM](src/MMSA/models/singleTask/MMIM.py)        |            [MMIM](https://github.com/declare-lab/Multimodal-Infomax)                   | EMNLP 2021        |

## 4. Supported MSA Models

|    Type     |                   Model Name                            |                                          From                                          |    Published      |
| :---------: | :-----------------------------------------------------: | :------------------------------------------------------------------------------------: | :---------------: |
| Single-Task |        [TFN](src/MMSA/models/singleTask/TFN.py)         |        [Tensor-Fusion-Network](https://github.com/A2Zadeh/TensorFusionNetwork)         | EMNLP 2017        |
| Single-Task |    [EF_LSTM](src/MMSA/models/singleTask/EF_LSTM.py)     |               [MultimodalDNN](https://github.com/rhoposit/MultimodalDNN)               | ACL 2018 Workshop |
| Single-Task |     [LF_DNN](src/MMSA/models/singleTask/LF_DNN.py)      |               [MultimodalDNN](https://github.com/rhoposit/MultimodalDNN)               | ACL 2018 Workshop |
| Single-Task |        [LMF](src/MMSA/models/singleTask/LMF.py)         | [Low-rank-Multimodal-Fusion](https://github.com/Justin1904/Low-rank-Multimodal-Fusion) | ACL 2018          |
| Single-Task |        [MFN](src/MMSA/models/singleTask/MFN.py)         |               [Memory-Fusion-Network](https://github.com/pliang279/MFN)                | AAAI 2018         |
| Single-Task |  [Graph-MFN](src/MMSA/models/singleTask/Graph_MFN.py)   |    [Graph-Memory-Fusion-Network](https://github.com/A2Zadeh/CMU-MultimodalSDK/)        | ACL 2018          |
| Single-Task | [MulT](src/MMSA/models/singleTask/MulT.py)(without CTC) |      [Multimodal-Transformer](https://github.com/yaohungt/Multimodal-Transformer)      | ACL 2019          |
| Single-Task |        [MFM](src/MMSA/models/singleTask/MFM.py)         |                     [MFM](https://github.com/pliang279/factorized/)                    | ICRL 2019         |
| Multi-Task  |     [MLF_DNN](src/MMSA/models/multiTask/MLF_DNN.py)     |                         [MMSA](https://github.com/thuiar/MMSA)                         | ACL 2020          |
| Multi-Task  |        [MTFN](src/MMSA/models/multiTask/MTFN.py)        |                         [MMSA](https://github.com/thuiar/MMSA)                         | ACL 2020          |
| Multi-Task  |        [MLMF](src/MMSA/models/multiTask/MLMF.py)        |                         [MMSA](https://github.com/thuiar/MMSA)                         | ACL 2020          |
| Multi-Task  |     [SELF_MM](src/MMSA/models/multiTask/SELF_MM.py)     |                      [Self-MM](https://github.com/thuiar/Self-MM)                      | AAAI 2021         |
| Single-Task |   [BERT-MAG](src/MMSA/models/singleTask/BERT_MAG.py)    |        [MAG-BERT](https://github.com/WasifurRahman/BERT_multimodal_transformer)        | ACL 2020          |
| Single-Task |       [MISA](src/MMSA/models/singleTask/MISA.py)        |                      [MISA](https://github.com/declare-lab/MISA)                       | ACMMM 2020        |
| Single-Task |       [MMIM](src/MMSA/models/singleTask/MMIM.py)        |            [MMIM](https://github.com/declare-lab/Multimodal-Infomax)                   | EMNLP 2021        |
| Single-Task |           BBFN (Work in Progress)                       |               [BBFN](https://github.com/declare-lab/BBFN)                              | ICMI 2021         |
| Single-Task |           [CENET](src/MMSA/models/singleTask/CENET.py)  |               [CENET](https://github.com/Say2L/CENet)                                  | TMM 2022          |
| Multi-Task  |     [TETFN](src/MMSA/models/multiTask/TETFN.py)         |                      TETFN                                                             | PR 2023           |  
| Single-Task |           [ALMT](src/MMSA/models/singleTask/ALMT.py)  |               [ALMT](https://github.com/Haoyu-ha/ALMT)                                  | EMNLP 2023          |


## 5. Results

Baseline results are reported in [results/result-stat.md](results/result-stat.md).
The preliminary results for EG-MISA and EG-MMIM compared to baseline models are shown in [result file](EG-results/results.md).


## 6. Citation

- [CH-SIMS: A Chinese Multimodal Sentiment Analysis Dataset with Fine-grained Annotations of Modality](https://www.aclweb.org/anthology/2020.acl-main.343/)
- [Learning Modality-Specific Representations with Self-Supervised Multi-Task Learning for Multimodal Sentiment Analysis](https://arxiv.org/abs/2102.04830)
- [M-SENA: An Integrated Platform for Multimodal Sentiment Analysis]()

Please cite our paper if you find our work useful for your research:

```
@inproceedings{yu2020ch,
  title={CH-SIMS: A Chinese Multimodal Sentiment Analysis Dataset with Fine-grained Annotation of Modality},
  author={Yu, Wenmeng and Xu, Hua and Meng, Fanyang and Zhu, Yilin and Ma, Yixiao and Wu, Jiele and Zou, Jiyun and Yang, Kaicheng},
  booktitle={Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics},
  pages={3718--3727},
  year={2020}
}
```

```
@inproceedings{yu2021learning,
  title={Learning Modality-Specific Representations with Self-Supervised Multi-Task Learning for Multimodal Sentiment Analysis},
  author={Yu, Wenmeng and Xu, Hua and Yuan, Ziqi and Wu, Jiele},
  booktitle={Proceedings of the AAAI Conference on Artificial Intelligence},
  volume={35},
  number={12},
  pages={10790--10797},
  year={2021}
}
```
