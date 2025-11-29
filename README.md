# HW3 – DDPM on MNIST 



---

## 0. 專案資料結構
請先將附件的檔案依下列資料結構擺放：
```bash
project_root/
├─ data/
│  ├─ 00001.png
│  ├─ 00002.png
│  └─ ...
├─ src/
│ ├─ train.py # 訓練
│ ├─ sample.py # 生成
│ ├─ model_unet.py # 簡化 U-Net 與 t-embedding
│ ├─ noise_schedule.py # β/α/α_bar 等表格預先計算
│ ├─ utils.py # 影像 I/O、種子、日誌工具等
│ ├─ dataset.py
│ ├─ ddpm.py
│ ├─ viz_diffusion.py #製作可視化
│ └─ train_ddpm.sh
├─ runs/專案名字/ckpt/ # 訓練權重輸出（會自動建立）
├─ mnist.npz #之後拿來用fid的
└─ readme.md

```


> **訓練資料**都放在 `data/`（或你自行指定路徑）。  
> 生成輸出（10,000 張）會放到你用 `out_imgs/` 

---

## 1. 建環境（Python 3.10）

### 1.1 使用 micromamba（建議）
```bash
curl micro.mamba.pm/install.sh | bash
source ~/.bashrc
micromamba create -n hw3 python=3.10
micromamba activate hw3
```
### 1.2 安裝白名單套件
```bash
pip install torch torchvision
pip install numpy pillow tqdm
pip install pytorch-fid
# 可選（若要畫 loss 曲線）：
# pip install tensorboard
```

## 2. 準備資料集
請將 MNIST（RGB 版，28×28）存成連號 png，放在：
```bash

data/mnist/
  ├── 00001.png
  ├── 00002.png
  └── ...
```

## 3. 訓練
schedule=1的時候是cosine，schedule=0是linear，可更改。
```bash
python -u src/train.py \
  --data-root ~project_root/data \
  --epochs 50 \
  --batch-size 128 \
  --lr 2e-4 \
  --logdir runs/ddpm_cosine_T1000_b512 \#或是自己改一下名字
  --save-every 20 \ #存中間的weight
  --num-workers 4 \
  --T 1000 \
  --schedule 1 \ #可改
  --seed 3407
```
輸出：
```bash
runs/ddpm_cosine_T1000_b512/ckpt/model_final.pth
```

## 4. 生成 10,000 張
這邊以我剛剛用的ddpm_cosine_T1000_b512為例，有別的記得改名字
```bash
python -u src/sample.py \
  --ckpt runs/ddpm_cosine_T1000_b512/ckpt/ddpm_epoch_50.pt \
  --n-samples 10000 \
  --out-dir out_imgs_cosine \ #等等資料夾的名字 可改
  --batch-size 256 \
  --T 1000
```
輸出結構：
```
out_imgs_cosine/ #剛剛用的名字
  ├── 00001.png
  ├── 00002.png
  └── ...
```
## 5. 計算 FID
方法一：與訓練資料夾直接比較
```bash
python -m pytorch_fid gen_images/ data/mnist/
```

方法二：與提供的 mnist.npz（預計算均值/共變異）比較
```bash
python -m pytorch_fid gen_images/ mnist.npz
```
