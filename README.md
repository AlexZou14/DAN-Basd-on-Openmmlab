# DAN-Basd-on-Openmmlab
DAN: [Unfolding the Alternating Optimization for Blind Super Resolution](https://arxiv.org/abs/2010.02631)

We reproduce DAN via [mmediting](https://github.com/open-mmlab/mmediting) based on [open-sourced code](https://github.com/greatlog/DAN).

## Requirements

- PyTorch >= 1.3
- mmediting >= 0.9

## DataSets
We use [DIV2K](https://data.vision.ee.ethz.ch/cvl/DIV2K/) and [Flickr2K](http://cv.snu.ac.kr/research/EDSR/Flickr2K.tar) as our training datasets.
For evaluation of Setting 2, we use [DIV2KRK](http://www.wisdom.weizmann.ac.il/~vision/kernelgan/DIV2KRK_public.zip) datasets,

## Usages
How to run this repo: copy the file to the mmediting workspace and run the program directly based on the commands in mmediting

1. Copy files to MMEditing workspace.
```shell
cd DAN-Basd-on-Openmmlab/
mv ./mmedit/models/restorers/dan.py ${mmediting_workspace}/mmedit/models/restorers/
mv ./mmedit/models/backbones/sr_backbones/dan_net.py ${mmediting_workspace}/mmedit/models/backbones/sr_backbones/
mv ./mmedit/models/common/DANpreprocess.py ${mmediting_workspace}/mmedit/models/common
mv ./configs/restorers/dan ${mmediting_workspace}/configs/restorers/
mv ./tools/data/super-resolution/dan_datasets ${mmediting_workspace}/tools/data/super-resolution/
```
2. Modify the configuration file as follows:

```python
pca_matrix_path='${mmediting_workspace}/tools/data/super-resolution/div2k/pca_matrix/pca_aniso_matrix_x4.pth' # your pca_matrix path
# Training
gt_folder='${dataset_workspace}/dataset/DF2K_train_HR_sub' # your train data path
# Testing
lq_folder='${dataset_workspace}/dataset/DIV2KRK/lr_x4' # your test data LR path
gt_folder='${dataset_workspace}/dataset/DIV2KRK/gt' # your test data HR path
```
3. Add script to init file, as follows:

- modify the `mmedit/models/backbones/sr_backbones/__init__.py`:
```python
from .dan_net import DAN
# add DAN into __all__ list.
```
- modify the `mmedit/models/commons/__init__.py`:
```python
from .dan_preprocess import SRMDPreprocessing
# add SRMDreprocessing into __all__ list.
```
- modify the `mmedit/models/restorers/__init__.py`:
```python
from .dan import DAN
# add DAN into __all__ list.
```

4. Training/Test

Before using it, please download and process the dataset and set the path in the configuration file.

- Train

```shell
# Single GPU
python tools/train.py configs/restorers/dan/dan_setting2.py --work_dir ${YOUR_WORK_DIR}

# Multiple GPUs
./tools/dist_train.sh configs/restorers/dan/dan_setting2.py ${GPU_NUM} --work_dir ${YOUR_WORK_DIR}
```

- Test
```shell
# Single GPU
python tools/test.py configs/restorers/dan/dan_setting2.py ${CHECKPOINT_FILE} [--metrics ${METRICS}] [--out ${RESULT_FILE}]

# Multiple GPUs
./tools/dist_test.sh configs/restorers/dan/dan_setting2.py ${CHECKPOINT_FILE} ${GPU_NUM} [--metrics ${METRICS}] [--out ${RESULT_FILE}]
```

## Result

### DIV2KRK
The passwds of download links are all 'ta2o'

| Method | scale | Datasets | PSNR | SSIM | Download |
| :-----: | :----: | :----: | :----: | :----: | :----:| 
| DAN-RGB (paper) | x4 | DIV2KRK | 26.09 | 0.7312 | - |
| DAN-Y (paper) | x4 | DIV2KRK | 27.55 | 0.7582 | - |
| DAN-RGB (Ours) | x4 | DIV2KRK | 27.41 | 0.7666 | [model](https://pan.baidu.com/s/1T_BOVR7Ui-NLUIKr6R20-w) / [test_pkl](https://pan.baidu.com/s/1T_BOVR7Ui-NLUIKr6R20-w) |
| DAN-Y (Ours) | x4 | DIV2KRK | 28.88 | 0.7915 | [model](https://pan.baidu.com/s/1T_BOVR7Ui-NLUIKr6R20-w) / [test_pkl](https://pan.baidu.com/s/1T_BOVR7Ui-NLUIKr6R20-w) |

------------
