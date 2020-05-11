English | [简体中文](./README.zh.md)

# DuEL_Baseline System
## Abstract
DuEL_Baseline is an EL(entity linking) baseline system developed for CCKS 2020 with our DuEL_Dataset. Different from CCKS 2019, 
CCKS 2020 has been expanded and improved on the basic EL task for Chinese short text. 
For detailed information about the dataset and task, please refer to our official [competition website](https://biendata.com/competition/ccks_2020_el/).
## Strategy

The DuEL model is a multi-task model, which contains two tasks: candidate entity ranking and prediction of mention type.

Candidate entity ranking task using pairwise model. In the training phase, the query and the description of the candidate entitys are 
feeded into the Ernie network to obtain the representation respectly. 
After that, the representation combined with the query and the description of the candidate entity is scored by 
the MLP. The loss function is rank_loss. 

The prediction of mention type task using classify model. In the training phase,  the query is feeded into the Ernie network to obtain the representation.
After that, the query representation vector passes through MLP to obtain the mention type. The loss function is classify loss.

Finally, we merge two loss functions for multi-task training.

In the prediction phase, we use the predicted type to verify the candidate entitys.

Note that, In the training and prediction phase, we treat NIL as an entity ID, see the code for details.
<img src="strategy.png" width="80%" height="80%">

We placed some examples of model input data under direction [./data/generated/](./data/generated), which can be generated by code [./data/data_process.py](./data/).
## Environments
Python2 + Paddle Fluid 1.5 (please confirm your Python path in scripts).

Dependencies are listed in ./requirements.txt.

The code is tested on a single P40 GPU, with CUDA version=10.0

## Download Dataset
Please download the training data, development data from the [competition website](https://biendata.com/competition/ccks_2020_el/data/), 
then unzip files into direction ./data/basic_data/

After decompression, the path ./data/basic_data/ contains files: 
    
    dev.json
    kb.json
    test.json
    train.json
    eval.py
    README
    CCKS 2020 Entity Linking License.docx

## Download pre-trained ERNIE model
Download ERNIE1.0 Base（max-len-512）model and extract it into ./pretrained_model/

    cd ./pretrained_mdoel/
    wget --no-check-certificate https://ernie.bj.bcebos.com/ERNIE_1.0_max-len-512.tar.gz
    tar -zxvf ERNIE_1.0_max-len-512.tar.gz


After decompression, the path ./pretrained_model/ERNIE_1.0_max-len-512 contains files: 
    
    ernie_config.json
    params
    vocab.txt

## Data Format Conversion
Generate data into ./data/generated/

    cd ./data/
    python data_process.py
    
## Training

    sh ./script/train.sh

By default the checkpoints will be saved into ./checkpoints/.
Accuracy and F1 are printed during the training procedure.

Please check python path and dataset path before training and prediction.

It is recommended to use less than 10,000 examples as verification set to reduce time consumption
## Prediction
Specify your checkpoints direction in the prediction script, and then run:

    sh ./script/predict.sh

This will write the predictions into a json file with the same format as the original dataset (required for final official evaluation). 
GPU ID and batch size can be specified in the script. The final prediction file is saved into ./data/generated/test_pred.json
## Official Evaluation
Modify the prediction result path in the evaluation file，and then run：

    python ./data/eval.py

## Copyright and License   
Copyright 2020 Baidu.com, Inc. All Rights Reserved

Licensed under the Apache License, Version 2.0 (the "License"); 
you may not use this file except in compliance with the License. You may otain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions 
and limitations under the License.
    
## APPENDIX
The data set contains 24 entity types, see the table below for details.

|Type| 中文名 |
|:---|:---|
|Event|	事件活动|
|Person|	人物|
|Work|	作品|
|Location|	区域场所|
|Time&Calendar|	时间历法|
|Brand|	品牌|
|Natural&Geography|	自然地理|
|Game|	游戏|
|Biological|	生物|
|Medicine|	药物|
|Food|	食物|
|Software|	软件|
|Vehicle|	车辆|
|Website|	网站平台|
|Disease&Symptom|	疾病症状|
|Organization|	组织机构|
|Awards|奖项|
|Education|	教育|
|Culture|	文化|
|Constellation|	星座|
|Law&Regulation|	法律法规|
|VirtualThings|	虚拟事物|
|Diagnosis&Treatment|	诊断治疗方法|
|Other|	其他|