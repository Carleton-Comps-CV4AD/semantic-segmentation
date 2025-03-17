# Semantic Segmentation on CARLA Data for CV4AD Carleton College Senior Thesis 
# Adapted from MIT ADE20K dataset in PyTorch


### Contributers

| Name           | Email                 |
| -------------- | --------------------- |
| Ben Zhao       | benzhao90@gmail.com   |
| Nathaniel Li   | lin@carleton.edu      |
| Julian Tanguma | tangumaj@carleton.edu |
| David Toledo   | toledod@carleton.edu  |
| Ethan Masadde(Indirectly)  | masaddee@carleton.edu |
| Josh Meier(Indirectly)    | meierj@carleton.edu   |



## Contents

- [Description](#description)
- [Background](#background)
- [Instructions](#instructions)
- [Reference](#reference)


## Description 
This is a PyTorch implementation of semantic segmentation models on custom data from the CARLA simulator across 4 different weather scenarios. Each dataset contains 1995 total images and our results are benchmarked from an 80-10-10 split for training, testing, and validation. There are 29 classes based on the the semantic segmentation camera in CARLA:(https://carla.readthedocs.io/en/latest/ref_sensors/#semantic-segmentation-camera). 
The implementation is adapted from the MIT ADE20K scene parsing dataset: 
(http://sceneparsing.csail.mit.edu/).


## Background
### ADE20k
ADE20K is the largest open source dataset for semantic segmentation and scene parsing, released by MIT Computer Vision team. Follow the link below to find the repository for their dataset and implementations on Caffe and Torch7:
https://github.com/CSAILVision/sceneparsing
### Our Project
We use semantic segmentation as a means for understanding how computer vision is affected by weather in terms of how an autonomous vehicle perceives its surroundings. We trained the model on CARLA data with a HRNETV2 encoder. This was adpated from the configurations provided in the ADE20K source code. 

Encoder:
- HRNetV2


Decoder:
- C1 (one convolution module)


### State-of-the-Art models
- **HRNet** is a recently proposed model that retains high resolution representations throughout the model, without the traditional bottleneck design. It achieves the SOTA performance on a series of pixel labeling tasks. Please refer to [https://arxiv.org/abs/1904.04514](https://arxiv.org/abs/1904.04514) for details.


## Instructions
### Environment
The code is developed under the following settings:
- Hardware: >=1 GPU for training, >=1 GPU for testing
- Software: Ubuntu (Version TBD), CUDA>=8.0, Python>=3.5, PyTorch>=0.4.0
Dependencies: numpy, scipy, opencv, yacs, tqdm

Step 1: Download CARLA Deterministic Data for 4 weathers (email cv4ad@gmail.com) or run datacollection.py from [cv4ad_CARLA_code](https://github.com/Carleton-Comps-CV4AD/cv4ad_CARLA_code)
to generatue custom data. Ensure that data is in a folder called new_data. We benchmark on 1995 total images per weather. 


Step 2: cd to the main folder

Step 3:
Install Miniconda
Setup Dependencies
```bash
conda create -n myenv python=python_version 
conda activate myenv
pip3 install torch, numpy, scipy, yacs, tqdm
```

### Training 
Step 4:
To train the model, first run the notebook to create an odgt for the weather scenario to be trained on: 
```bash
/root/scripts/mixeddatapreprocessing.ipynb
```
Move odgt files to new_data folder. 

Step 5:
Train our model by selecting the number of ($GPUS) and configuration file ($CFG). During training, checkpoints by default are saved in folder called ckpt.
Our base configuration files can be found in /root/cfg. Ensure that data directories in configuration files point to where odgt files are located for training and validation. 
Example(Train on clear day): 
```bash
python3 train.py --gpu 0 --cfg config/ade20k-hrnetv2-CARLADAYCLEAR.yaml
```
GPUS can be a specified number from 0-7 or a specific subset(0,1,3,5). 

### Testing(without metrics) 
Step 6:
Testing without metrics can be done by setting the test image folder under ($IMGS):
```bash
python3 -u test.py --imgs testrandomimages/ --gpu 0 --cfg config/ade20k-mobilenetv2dilated-c1_deepsup-custom.yaml
```

### Evaluation(with metrics) 
Step 7:
Set flag for visualization to be True for three column outputs. From left to right(RGB Image, GT, Prediction):
```bash
python3 eval_multipro.py --gpus 0 --cfg config/ade20k-hrnetv2-CARLADAYCLEAR.yaml --test_set ./new_data/odgt_foggy_day
```
By default, you can omit $TEST_SET flag for each weather to test on own weather. You can also test on any other weather by including the flag. 

## Reference

Semantic Understanding of Scenes through ADE20K Dataset. B. Zhou, H. Zhao, X. Puig, T. Xiao, S. Fidler, A. Barriuso and A. Torralba. International Journal on Computer Vision (IJCV), 2018. (https://arxiv.org/pdf/1608.05442.pdf)

    @article{zhou2018semantic,
      title={Semantic understanding of scenes through the ade20k dataset},
      author={Zhou, Bolei and Zhao, Hang and Puig, Xavier and Xiao, Tete and Fidler, Sanja and Barriuso, Adela and Torralba, Antonio},
      journal={International Journal on Computer Vision},
      year={2018}
    }

Scene Parsing through ADE20K Dataset. B. Zhou, H. Zhao, X. Puig, S. Fidler, A. Barriuso and A. Torralba. Computer Vision and Pattern Recognition (CVPR), 2017. (http://people.csail.mit.edu/bzhou/publication/scene-parse-camera-ready.pdf)

    @inproceedings{zhou2017scene,
        title={Scene Parsing through ADE20K Dataset},
        author={Zhou, Bolei and Zhao, Hang and Puig, Xavier and Fidler, Sanja and Barriuso, Adela and Torralba, Antonio},
        booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
        year={2017}
    }
    
