# MER2024 Baseline

## Dataset
To download the dataset, please fill out an [EULA](https://drive.google.com/file/d/1cXNfKHyJzVXg_7kWSf_nVKtsxIZVa517/view?usp=sharing) and send it to our official email address merchallenge.contact@gmail.com. It requires participants to use this dataset only for academic research and not to edit or upload samples to the Internet.


## Paper

[**MER 2024: Semi-Supervised Learning, Noise Robustness, and Open-Vocabulary Multimodal Emotion Recognition**](https://arxiv.org/abs/2404.17113)<br>
Zheng Lian, Haiyang Sun, Licai Sun, Zhuofan Wen, Siyuan Zhang, Shun Chen, Hao Gu, Jinming Zhao, Ziyang Ma, Xie Chen, Jiangyan Yi, Rui Liu, Kele Xu, Bin Liu, Erik Cambria, Guoying Zhao, Björn W. Schuller, Jianhua Tao<br>

Please cite our paper if you find our work useful for your research:

```tex
@inproceedings{lian2023mer,
  title={MER 2024: Semi-Supervised Learning, Noise Robustness, and Open-Vocabulary Multimodal Emotion Recognition},
  author={Lian, Zheng and Sun, Haiyang and Sun, Licai and Wen, Zhuofan and Zhang, Siyuan and Chen, Shun and Gu, Hao and Zhao, Jinming and others},
  booktitle={arXiv:2404.17113},
  year={2024}
}
```



## Usage
This is an extension of **./MER2023** and **./MERBench**. You can also refer to the files in these folders for more details.



### Create ./tools folder

```shell
## for face extractor (OpenFace-win)
https://drive.google.com/file/d/1-O8epcTDYCrRUU_mtXgjrS3OWA4HTp0-/view?usp=share_link  -> tools/openface_win_x64
## for visual feature extraction
https://drive.google.com/file/d/1DZVtpHWXuCmkEtwYJrTRZZBUGaKuA6N7/view?usp=share_link ->  tools/ferplus
https://drive.google.com/file/d/1wT2h5sz22SaEL4YTBwTIB3WoL4HUvg5B/view?usp=share_link ->  tools/manet
https://drive.google.com/file/d/1-U5rC8TGSPAW_ILGqoyI2uPSi2R0BNhz/view?usp=share_link ->  tools/msceleb
https://drive.google.com/file/d/1sg-sI8QgFJwfPfWC1fQkVXDVvoQiGbxY/view?usp=sharing    ->  tools/videomae-base-VoxCeleb2/checkpoint-99.pth
https://drive.google.com/file/d/14TpZHsdICfce36rw6j3oB_hdbpdX6DHK/view?usp=sharing    ->  tools/videomae-base-K400-mer2023/checkpoint-299.pth

## for audio extraction
https://www.johnvansickle.com/ffmpeg/old-releases ->  tools/ffmpeg-4.4.1-i686-static
## for acoustic acoustic features
https://drive.google.com/file/d/1I2M5ErdPGMKrbtlSkSBQV17pQ3YD1CUC/view?usp=share_link ->  tools/opensmile-2.3.0
https://drive.google.com/file/d/1Q5BpDrZo9j_GDvCQSN006BHEuaGmGBWO/view?usp=share_link ->  tools/vggish

## ASR: download wenet model and move to tools/wenet => you can use other ASR toolkits
visit "https://github.com/wenet-e2e/wenet/blob/main/docs/pretrained_models.en.md" fill the request link and download
"https://wenet-1256283475.cos.ap-shanghai.myqcloud.com/models/wenetspeech/wenetspeech_u2pp_conformer_libtorch.tar.gz"

## huggingface for multimodal feature extracion
## We take chinese-hubert-base for example, all pre-trained models are downloaded to tools/transformers. The links for different feature extractos involved in MERBench, please refer to Table18 in our paper.
https://huggingface.co/TencentGameMate/chinese-hubert-base    -> tools/transformers/chinese-hubert-base
```



### Dataset Preprocessing

(1) Download the dataset and put it into **./mer2024-dataset**

(2) We provide the code for dataset preprocessing.

```shell
# see toolkit/preprocess/mer2024.py
```

(3) Feature extractions

Please refer to **run-mer2024.sh** for more details.

You can choose feature_level in ['UTTERANCE', 'FRAME'] to extract utterance-level or frame-level features.



### Unimodal Baseline

1. You can also change the feature names, we take **eGeMAPS_UTT** for example.
2. By default, we randomly select hyper-parameters during training. Therefore, please run each command line 50 times and choose the best hyper-parameters.

~~~~shell
python -u main-release.py --model='attention' --feat_type='utt' --dataset=MER2024 --audio_feature=eGeMAPS_UTT --text_feature=eGeMAPS_UTT --video_feature=eGeMAPS_UTT --gpu=0
~~~~



### Multimodal Benchmark

1. Adjust AUDIO_RANK_LOW2HIGH  / TEXT_RANK_LOW2HIGH / IMAGR_RANK_LOW2HIGH  in **toolkit/globals.py**
2. Training => automatic selection top-n features for each modality

```shell
## [fusion_topn 1~6; fusion_modality in ['AV', 'AT', 'VT', 'AVT']]
python -u main-release.py --model=attention_topn --feat_type='utt' --dataset=MER2024 --fusion_topn=5 --fusion_modality='AVT' --gpu=0
```



### Others

We provide the code about how to generate noise samples => [for MER-NOISE]

```shell
## for audio corruption (only use the speech subset)
https://www.openslr.org/17/ -> tools/musan

## noise sample generation
python main-check.py main_mixture_multiprocess 
            --video_root='/share/home/lianzheng/chinese-mer-2023/dataset/mer2024-dataset-process/video-test2' 
            --save_root='/share/home/lianzheng/chinese-mer-2023/dataset/mer2024-dataset-process/video-test2-noise' 
```

## Acknowledgement

Thanks to [Hugging Face](https://huggingface.co/docs/transformers/index), [MMSA](https://github.com/thuiar/MMSA), [pytorch](https://github.com/pytorch/pytorch), [openface](https://github.com/TadasBaltrusaitis/OpenFace), [fairseq](https://github.com/facebookresearch/fairseq)
