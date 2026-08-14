# VisG AV-HuBERT (Viseme-Guided Audio-Visual Hidden Unit BERT)
<!--
[Learning Audio-Visual Speech Representation by Masked Multimodal Cluster Prediction](https://arxiv.org/abs/2201.02184)

[Robust Self-Supervised Audio-Visual Speech Recognition](https://arxiv.org/abs/2201.01763)


-->
## Introduction
![example](example.png)
VisG AV-HuBERT is a multitask extension of AV-HuBERT designed to better leverage the visual modality by incorporating viseme classification as an auxiliary objective. 
Compared to AV-HuBERT, our model achieves comparable or improved performance—particularly under challenging noise conditions (-10 dB, -5 dB). Find the paper [here](https://arxiv.org/abs/2604.00982). We are very glad to announce that the paper has been accepted for publication at the 28th International Conference on Pattern Recogniton (ICPR 2026).

If you find VisG AV-HuBERT useful in your research, please use the following BibTeX entry for citation.

```BibTeX
@InProceedings{10.1007/978-3-032-31335-5_45,
author="Papadopoulos, Aristeidis and Jain, Rishabh and Harte, Naomi",
title="VisG AV-HuBERT: Viseme-Guided AV-HuBERT",
booktitle="Pattern Recognition",
year="2026",
publisher="Springer Nature Switzerland",
pages="667--682",
}
```
<!--

## License

AV-HuBERT LICENSE AGREEMENT

This License Agreement (as may be amended in accordance with this License
Agreement, “License”), between you (“Licensee” or “you”) and Meta Platforms,
Inc. (“Meta” or “we”) applies to your use of any computer program, algorithm,
source code, object code, or software that is made available by Meta under this
License (“Software”) and any specifications, manuals, documentation, and other
written information provided by Meta related to the Software (“Documentation”).

By using the Software, you agree to the terms of [this
License](https://github.com/facebookresearch/av_hubert/blob/main/LICENSE). If
you do not agree to this License, then you do not have any rights to use the
Software or Documentation (collectively, the “Software Products”), and you must
immediately cease using the Software Products.

## Pre-trained and fine-tuned models

Please find the checkpoints [here](http://facebookresearch.github.io/av_hubert)

## Demo
Run our lip-reading demo using Colab: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1bNXkfpHiVHzXQH8WjGhzQ-fsDxolpUjD)
-->
## Model Checkpoints:
Please find our viseme-guided models [here](https://drive.google.com/drive/folders/1_C-nTn70q745HgS_BF7kAD9xb_H9Prdr?usp=drive_link). We provide the base and the large VisG AV-HuBERT, fine-tuned as described in the paper.


## Installation
First, follow AV-HuBERT installation instructions from [AV-HuBERT](https://github.com/facebookresearch/av_hubert).

After completing those steps, downgrade the following packages to ensure compatibility:
```
pip install pip==24.0 pip install omegaconf==2.0.4 pip install hydra-core==1.0.7 pip install numpy==1.23.0
```
To pre-process the dataset and generate phoneme and viseme transcriptions, Montreal Forced Aligner (MFA) is used along with Grapheme-to-Phoneme(https://pypi.org/project/g2p-en/). Run the following:
```
pip install montreal-forced-alinger=2.0.2 pip install g2p-en==2.1.0
```
Follow instructions from [here](https://montreal-forced-aligner.readthedocs.io/en/v3.2.3/installation.html).
We use the General American [Dictionary](https://mfa-models.readthedocs.io/en/latest/dictionary/English/English%20%28US%29%20ARPA%20dictionary%20v2_0_0.html) and the corresponding acoustic model.

## Train a new model
To pre-train a new model from scratch, follow the detailed guide of [AV-HuBERT](https://github.com/facebookresearch/av_hubert).

### Data preparation 

Follow the steps in [`preparation`](avhubert/preparation/) to pre-process:
- LRS3 and VoxCeleb2 datasets
- MUSAN dataset for noise augmented audio-files

Select the English dialect to be used for alignment, along with the acoustic model from the Montreal Forced Aligner Documentation and
download those files using:
```sh
mfa model download dictionary your_selection
mfa model download acoustic your_selection
```
Then perform alignment by following the Montreal Forced Aligner Documentation. Run the [label_mapping.py](avhubert/label_mapping.py)
to create the viseme manifest files and viseme dictionary file. Currently, Lee's mapping is supported.


We additionally use LRS2 to evaluate generalisation capabilities.
- We follow the guide provided [here](https://github.com/rishabhjain16/lipreading-data-guide/tree/main/LRS2) for initial pre-processing.
- In [`preparation\LRS2`](avhubert/preparation/LRS2), we use lrs2_scale.py to normalize the audio files of LRS2.
- Then, run lrs2_noise.py file to create noise files in a similar manner to LRS3.

### Finetune with Viseme-Guidance AV-HuBERT model
Suppose `{train,valid}.tsv` are saved at `/path/to/data`, `{train,valid}.wrd`
are saved at `/path/to/labels`, the configuration file is saved at `/path/to/conf/conf-name`.

To fine-tune a pre-trained HuBERT model at `/path/to/checkpoint`, run:
```sh
$ cd avhubert
$ bash scripts/training.sh
```

### Decode an AV-HuBERT model
Suppose the `test.tsv` and `test.wrd` are the video list and transcripts of
the split to be decoded, saved at `/path/to/data`, and the fine-tuned model is
saved at `/path/to/checkpoint`. Run the following:

```sh
$ cd avhubert
$ bash scripts/decode.sh
```

#### Test under noisy environment
##### New: Find the noise files we used [here](https://drive.google.com/drive/folders/1ZnmBQSJ4wiffdltgWyYeWO_NElTJh65M?usp=sharing)

If you want to test your model under noisy environment, run the following command, to evaluate under all noise categories (Babble, Speech, Music, Noise).
See [`preparation`](avhubert/preparation/) for setting up noise directories. The following script evaluates all categories, from -10dB to 10dB SNR and saves the results in a csv file.
```sh
$ cd avhubert
$ bash scripts/mass_decode.sh
```
# Licence
Our work is licensed under BSD-3. Please, take care of the licenses of the modules we build upon, including AV-HuBERT, Montreal Forced Aligned and LRS3.


