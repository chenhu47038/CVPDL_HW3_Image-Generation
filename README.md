# HW3 – DDPM on MNIST (from scratch)



---

## 0. 專案資料結構（訓練與取樣所需）
```bash
project_root/
├─ data/
│ └─ mnist/ # MNIST 影像（RGB, 28x28, 以 png 命名）
│ ├─ 00001.png
│ ├─ 00002.png
│ └─ ...
├─ src/
│ ├─ train.py # 訓練
│ ├─ generate.py # 取樣（生成 10k）
│ ├─ model_unet.py # 簡化 U-Net 與 t-embedding
│ ├─ schedules.py # β/α/α_bar 等表格預先計算
│ └─ utils.py # 影像 I/O、種子、日誌工具等
├─ ckpt/ # 訓練權重輸出（會自動建立）
└─ readme.md
```


> **訓練資料**與**FID 比對資料**都放在 `data/mnist/`（或你自行指定路徑）。  
> 取樣輸出（10,000 張）會放到你用 `--outdir` 指定的資料夾（例如 `gen_images/`）。

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

### 2. 準備資料集
請將 MNIST（RGB 版，28×28）存成連號 png，放在：
```bash

data/mnist/
  ├── 00001.png
  ├── 00002.png
  └── ...
```

### 3. 訓練
```bash
python src/train.py \
  --data_path data/mnist \
  --epochs 100 \
  --batch_size 128 \
  --lr 1e-4 \
  --save_dir ckpt/
```
輸出：
```bash
ckpt/model_final.pth
```

### 4. 取樣（生成 10,000 張）
```bash
python src/generate.py \
  --ckpt ckpt/model_final.pth \
  --num_images 10000 \
  --outdir gen_images/
```
輸出結構：
```
gen_images/
  ├── 00001.png
  ├── 00002.png
  └── ...
```
### 5. 計算 FID
方法一：與訓練資料夾直接比較
```bash
python -m pytorch_fid gen_images/ data/mnist/
```

方法二：與提供的 mnist.npz（預計算均值/共變異）比較
```bash
python -m pytorch_fid gen_images/ mnist.npz
```
