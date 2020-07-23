## About
Repository for paper titled [*"Stacked DeBERT: All Attention in Incomplete Data for Text Classification"*](https://arxiv.org/abs/2001.00137).

## Contents
[Overview](#overview) • [Requirements](#requirements) • [How to Use](#how-to-use) • [How to Cite](#acknowledgment)

## Overview
* Proposed model: Stacked Denoising BERT 
    <p align="left">
    <img src="./data/assets/model.png" height="250" alt="Model Architecture">
    </p>
    
* Task: Text classification from noisy data
    * Twitter Sentiment Classification
    * Incomplete Intent Classification: text with STT error

* Baseline models
  * [BERT](https://github.com/huggingface/pytorch-pretrained-BERT)
  * NLU Platforms: [Rasa](https://rasa.com) (spacy and tf), [Dialogflow](https://dialogflow.com), and [SAP Conversational AI](https://cai.tools.sap) 
  * [Semantic Hashing with Classifier](https://github.com/kumar-shridhar/Know-Your-Intent)

## Requirements
Python 3.6 (3.7.3 tested), PyTorch 1.0.1.post2, CUDA 9.0 or 10.1
```
pip install --default-timeout=1000 torch==1.0.1.post2
pip install -r requirements.txt
conda install pytorch torchvision cudatoolkit=9.0 -c pytorch
```

## How to Use
### 1. Dataset
* Chatbot NLU Evaluation Benchmark dataset with missing/incorrect data (STT errors) and Twitter Sentiment dataset (Check [Dataset README](./data/README.md))
* Training done on:
    * Twitter dataset: complete data, incomplete data, complete+incomplete data
    * Chatbot dataset: complete data, 2 TTS-STT data (gtts-witai, macsay-witai)

### 2. Pre-fine-tune BERT
* Twitter Sentiment Corpus
```
CUDA_VISIBLE_DEVICES=0,1 ./scripts/twitter_sentiment/run_bert_classifier_inc_with_corr.sh
```
> Script for *Inc+Corr* dataset. Scripts corresponding to *Inc* and *Corr* are also available in the same folder.

* Chatbot Incomplete Intent Corpus: texts with STT Error
```
CUDA_VISIBLE_DEVICES=0,1 ./scripts/stterror_intent/run_bert_classifier_stterror.sh
```
> Script for noisy data (*stterror*). Script for clean, non-noisy data, is also available (*complete*).

### 3. Train/test model
* Training on Twitter Corpus
```
CUDA_VISIBLE_DEVICES=0,1 ./scripts/twitter_sentiment/run_stacked_debert_dae_classifier_twitter_inc_with_corr.sh
```
> Make sure the OUTPUT directory is the same as the fine-tuned BERT or copy the BERT model to your new output dir.

* Training on NLU Evaluation Corpora for TTS=gtts/macsay and STT=witai and autoencoder epochs 100-1000.
```
CUDA_VISIBLE_DEVICES=0,1 ./scripts/stterror_intent/run_stacked_debert_dae_classifier_stterror.sh
```

### 4. Test model
* Testing on NLU Evaluation Corpora
```
CUDA_VISIBLE_DEVICES=0 python run_stacked_debert_dae_classifier.py --seed 1 --task_name "sentiment140_sentiment" --save_best_model --do_eval --do_lower_case --data_dir ./data/twitter_sentiment_data/sentiment140/ --bert_model bert-base-uncased --max_seq_length 128 --train_batch_size 4 --eval_batch_size 1 --learning_rate 2e-5 --num_train_epochs_autoencoder 3 --num_train_epochs 3 --output_dir_first_layer "./results/test/results_stacked_debert_dae_earlyStopWithEvalLoss_twitter_10seeds/inc_with_corr_sentences_TestOnlyIncorrect/sentiment140_ep3_bs4_inc_with_corr_TestOnlyIncorrect_seed1_first_layer_epae1000/" --output_dir "./results/test/results_stacked_debert_dae_earlyStopWithEvalLoss_twitter_10seeds/inc_with_corr_sentences_TestOnlyIncorrect/sentiment140_ep3_bs4_inc_with_corr_TestOnlyIncorrect_seed1_second_layer_epae1000/"
```

## Acknowledgment
In case you wish to use this code, please use the following citation:

```
@misc{cunha2020stackeddebert,
   title={Stacked DeBERT: All Attention in Incomplete Data for Text Classification},
   author={Gwenaelle Cunha Sergio and Minho Lee},
   year={2020},
   eprint={2001.00137},
   archivePrefix={arXiv},
   primaryClass={cs.CL}
}
```
> Email for further requests or questions: `gwena.cs@gmail.com`

The authors would like to thank Snips.co and Kaggle for their public datasets ([Snips NLU Benchmark](https://github.com/snipsco/nlu-benchmark) and [Sentiment140 Twitter Dataset](https://www.kaggle.com/kazanova/sentiment140)), and HuggingFace's [BERT PyTorch code](https://github.com/huggingface/pytorch-pretrained-BERT).
