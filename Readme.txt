## 1. Environment

First, create a new environment and install the requirements:
```shell
conda create -n genericssl python=3.8
conda activate genericssl
cd GenericSSL/
pip install -r requirements.txt
```

[**📌IMPORTANT**] Then, before running the code, set the `PYTHONPATH` to `pwd`:
```shell
export PYTHONPATH=$(pwd)/code:$PYTHONPATH
```

## 2. Data Preparation

First, download the datasets and put them under the `Datasets` folder:

The file structure should be: 
```shell
.
├── Datasets
│   ├── SubDatasets1
│   │   ├── patient_1
│   │   │   ├── main.nii.gz
│   │   │   │── liver.nii.gz
│   │   │   ├── portal.nii.gz
│   │   │   ├── vein.nii.gz
│   │   │   └── ...
│   │   └── ...
│   ├── SubDatasets2
│   │   ├── patient_121
│   │   │   ├── main.nii.gz
│   │   │   │── liver.nii.gz
│   │   │   ├── portal.nii.gz
│   │   │   ├── vein.nii.gz
│   │   │   └── ...
│   │   └── ...
│   ├── SubDatasets3
│   │   ├── 24
│   │   │   ├── main.nii.gz
│   │   │   │── liver.nii.gz
│   │   │   ├── portal.nii.gz
│   │   │   ├── vein.nii.gz
│   │   │   └── ...
│   │   └── ...
│   ├── SubDatasets4
        ├── 033
        │   ├── main.nii.gz
        │   │── liver.nii.gz
        │   ├── portal.nii.gz
        │   ├── vein.nii.gz
        │   └── ...
        └── ...

```

### 2.1 Pre-process LASeg dataset
Run `python ./code/data/process_data.py` to:
- crop main.nii.gz by liver mask
- generate laplacian_enhancement
- generate skeleton
- convert and transpose .nii to .npy


### 2.2 split dataset
Run `python ./code/data/split_data.py` to:
- generate the train/test splits
- generate the labeled/unlabeled splits.


Finally, you will get a file structure as follow:
```shell
.
├── SubDataset1
│   ├── patient_1
│   │   ├── main.npy
│   │   ├── portal.npy
│   │   └── ...
├── SubDataset2
│   ├── patient_1
│   │   ├── main.npy
│   │   ├── portal.npy
│   │   └── ...
├── SubDataset3
│   ├── patient_1
│   │   ├── main.npy
│   │   ├── portal.npy
│   │   └── ...
├── SubDataset4
│   ├── patient_1
│   │   ├── main.npy
│   │   ├── portal.npy
│   │   └── ...
├── split_txts
│   ├── eval_toA_0.1.txt
│   │── train_toA_labeled_0.1.txt
│   │── train_toA_unlabeled_0.1.txt
│   │── ...
```


## 3. Training & Testing & Evaluating

### 3.1 Training

```shell
python ./code/train_diffusion.py -g 0 -exp portal -ep 1200 --base_lr 0.001 -sl train_toD_labeled_0.1 -su train_toD_unlabeled_0.1 -se test_toD_0.1
```
Parameters:

`-g`: use which gpu to train

`--exp`: `portal` means portal prediction, `vein` means vein prediction, `full` means using combined label(portal and vein) for prediction,

`-ep`: max epochs

`--base_lr`: learning rate

`-sl`: train_labeled_split, `10%` labeled, setting: `train_labeled_0.1`

`-su`: train_unlabeled_split, `10%` unlabeled, setting: `train_unlabeled_0.1`

`-se`: eval_split, `10%` labeled and test D, setting: `test_toD_0.1`

### 3.2 Eval

```shell
python ./code/test.py
python ./code/evaluate.py
```
