# 电表检测数据集

## 数据说明
- **类别**: electricity meter (电表)
- **样本数量**: 100张增强图片
- **标注格式**: YOLO (class x_center y_center width height)

## 目录结构
```
meter_dataset_sample/
├── images/          # 100张增强图片 (meter_000.jpg ~ meter_099.jpg)
├── labels/          # 100个YOLO标注文件 (meter_000.txt ~ meter_099.txt)
└── visualize/       # 可视化样本 (带红框标注)
```

## 增强方式
- 亮度/对比度/饱和度调整
- 高斯噪声/模糊
- 随机裁剪/缩放
- 水平翻转
- 小角度旋转 (-15°~+15°)
- 透视变换
- 随机阴影

## YOLO 训练配置

### data.yaml
```yaml
path: ./meter_dataset
train: images
test: images  # 建议自己划分验证集

nc: 1
names: ['electricity meter']
```

### 数据集划分脚本
```python
import os
import random
import shutil

images = sorted([f for f in os.listdir('images') if f.endswith('.jpg')])
random.seed(42)
random.shuffle(images)

split = int(len(images) * 0.8)
train_imgs = images[:split]
val_imgs = images[split:]

# 创建目录
os.makedirs('train/images', exist_ok=True)
os.makedirs('train/labels', exist_ok=True)
os.makedirs('val/images', exist_ok=True)
os.makedirs('val/labels', exist_ok=True)

for img in train_imgs:
    label = img.replace('.jpg', '.txt')
    shutil.copy(f'images/{img}', 'train/images/')
    shutil.copy(f'labels/{label}', 'train/labels/')

for img in val_imgs:
    label = img.replace('.jpg', '.txt')
    shutil.copy(f'images/{img}', 'val/images/')
    shutil.copy(f'labels/{label}', 'val/labels/')
```

## 生成方法
基于单张电表图片，使用 Grounding DINO + 数据增强生成。
