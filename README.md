# CVPDL_HW3_Image-Generation



````markdown
# HW3 – Diffusion Model for MNIST Image Generation

## 1. Environment Setup

### (1) Install micromamba
```bash
curl micro.mamba.pm/install.sh | bash
source ~/.bashrc
````

### (2) Create environment

```bash
micromamba create -n hw3 python=3.10
micromamba activate hw3
```

### (3) Install required packages

```bash
pip install torch torchvision
pip install numpy pillow tqdm
pip install pytorch-fid
```

---

## 2. Dataset Preparation

請先下載 MNIST RGB 版本（作業提供 Google Drive）：
放置於：

```
data/mnist/
    ├── 00001.png
    ├── 00002.png
    ...
```

README 不要附資料集，依照作業規範說明路徑即可。

---

## 3. Training

將所有程式碼放在 `src/` 資料夾。
訓練指令：

```bash
python src/train.py \
    --data_path data/mnist \
    --epochs 100 \
    --save_dir ckpt/
```

模型會輸出：

```
ckpt/model_final.pth
```

---

## 4. Generate 10,000 Images

```bash
python src/generate.py \
    --ckpt ckpt/model_final.pth \
    --num_images 10000 \
    --outdir gen_images/
```

輸出格式需符合作業要求：

```
gen_images/
    ├── 00001.png
    ├── 00002.png
    ...
```

之後 zip 成：

```
img_<student-id>.zip
```

---

## 5. Compute FID

### 方法一：與 MNIST 原圖比較

```bash
python -m pytorch_fid gen_images/ data/mnist/
```

### 方法二：使用 mnist.npz（作業提供）

```bash
python -m pytorch_fid gen_images/ mnist.npz
```

---

## 6. File Structure for Submission

```
hw3_<student-id>.zip
│── hw3_<student-id>/
│     ├── report_<student-id>.pdf
│     ├── code_<student-id>.zip
│     │      └── src/*
│     ├── img_<student-id>.zip
│           └── 00001.png ~ 10000.png
```

---

## 7. Notes

* 不得使用 pretrained model
* 不得使用外部 dataset
* Python >= 3.10
* 請遵守作業格式，否則會被扣分

```

---

如果你需要：

✅ 我幫你把 **report 轉成 PDF**  
✅ 幫你做 **diffusion 過程 8×8 圖**  
✅ 幫你畫 **DDPM 架構圖（照我上面寫的 prompt）**  
✅ 幫你把你的程式碼寫成更乾淨完整版本  

只要告訴我，我都能立即幫你完成。
```
