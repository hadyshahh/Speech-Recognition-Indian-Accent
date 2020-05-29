# Indian Accent Speech Recognition
Traditional ASR (Signal & Cepstral Analysis, DTW, HMM) & DNNs (Custom Models + DeepSpeech) on Indian Accent Speech

To understand the context, theory and explanation of this project, head over to my blog:
https://medium.com/@AnandAI/self-driving-car-on-indian-roads-4e305cb04198


# How to Use?

## Contents:

- vui_notebook.ipynb: **DNN Custom Models and Comparative Analysis* to make a custom Speech Recognition model.
- DeepSpeech_Training.ipynb: **Retraining of DeepSpeech Model** with Indian Accent Voice Data.
- Training_Instructions.docx: Instructions to train DeepSpeech model.

## Data Source/ Training Data:

**Indic TTS Project:** Downloaded 50+ GB of Indic TTS voice DB from Speech and Music Technology Lab,  IIT Madras, which comprises of 10000+ spoken sentences from 20+ states (both Male and Female native speakers)

https://www.iitm.ac.in/donlab/tts/index.php

You can also record your own audio or let the ebook reader apps read a document. But I found it is insufficient to train such a heavy model. Then I requested support of IIT Madras, Speech Lab who kindly granted access to their Voice database.

## DNN Custom Models for Speech Recognition:

### Model 1: CNN + RNN + TimeDistributed Dense

![](images/Model1.jpg)

### Model 2: Deeper RNN + TimeDistributed Dense

![](images/Model2.jpg)

### Comparison: Training Loss & Validation Loss of Model 1 (CNN) & Model 2 (RNN)

![](images/comparison.jpg)

### Model 3: Pooled CNN+Deep Bidirectional RNN +Time-distributed Dense

![](images/Model3.jpg)


## DeepSpeech Model Training:

These are the high level steps we gonna do:
- Get a **pre-trained model.**
- Load **Indian Accent English Speech dataset**
- **Convert to the input format** to feed the DeepSpeech model.
- Compare trained model with DeepSpeech base model to validate improvement.


## Step by step instructions

- The dataset contains the audio and its description. But to load the data to deep speech model, we need to generate CSV containing audio file path, its transcription and file size.
- **Split the CSV file** into 3 parts: test.csv,train.csv and valid.csv.
- Write a **python program to set the frame rate** for all audio files into 12000hz (deep speech model requirement)
- Clone the **Baidu DeepSpeech Project** 0.5.1 from here
- **Execute DeepSpeech.py** with appropriate parameters (given below).
- Export_dir will contain output_graph.pbmm which you load in deepspeech.model() function.
- **KenLM ToolKit** is used to generate Trie file. It is required to pass in to deep speech decoder function.
- **model.enableDecoderWithLM(lm_file,trie,0.75,1.85):** lm_file is the .pbmm after training and trie is the output of KenLM Toolkit.
- Use deep speech decoder function to do STT.

```
./DeepSpeech.py --train_files ../data/CV/en/clips/train.csv --dev_files ../data/CV/en/clips/dev.csv --test_files ../data/CV/en/clips/test.csv
```

To fine-tune the entire graph using data in train.csv, dev.csv, and test.csv, for 3 epochs we can tune hyperparameters as below,
```
python3 DeepSpeech.py --n_hidden 2048 --checkpoint_dir path/to/checkpoint/folder --epochs 3 --train_files my-train.csv --dev_files my-dev.csv --test_files my_dev.csv --learning_rate 0.0001
```

**Hyperparameters for Training:**

```
python -u DeepSpeech.py \
   --train_files /home/prem/ds_project/datavoice/data_voice_train.csv \
   --test_files /home/prem/ds_project/datavoice/data_voice_test.csv \
   --dev_files /home/prem/ds_project/datavoice/data_voice_dev.csv \
   --n_hidden 2048 \
   --epoch 100 \
   --use_seq_length False \
   --checkpoint_dir /home/prem/ds_project/datavoice/checkpoints/ \
   --learning_rate 0.0001 \
   --export_dir /home/prem/ds_project/datavoice/model_export/ \
   --train_batch_size 64 \
   --test_batch_size 32 \
   --dev_batch_size 32 \
```


## Comparing Indian Accent English Model with Deepspeech model
To check accuracy, we used **3 metrics: WER, WACC and BLUE SCORE.**

![](images/metrics.jpg)

Metric shows trained model **performs much better for Indian Accent English.**

## Model Comparison Results:

Lets plot above metrics, feeding Indian Accent Speech Data (Test Set) to both DeepSpeech pre-trained model and our trained model to compare. The **3 bins in graphs below represents low, medium and high accuracy, from left to right.**

### DeepSpeech Base Model: Most datapoints classified as "Low Accuracy" in all 3 metrics
![](images/graph1.jpg)

### Trained Model: Most datapoints classified as "Medium & High Accuracy" in all 3 metrics
![](images/graph2.jpg)

The above depiction proves that the **trained model performs much better for Indian Accent Speech Recognition** compared to DeepSpeech model.