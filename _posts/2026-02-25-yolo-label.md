---
title: "Image Labeling for YOLO with labelImg"
author: "Raymond"
date: 2026-02-19 18:00:00 +0900
categories: [Tips]
tags: [YOLO,labelImg]
---

## EXIF Orientation
Smartphone cameras often store images in a fixed orientation and record the actual viewing direction in the EXIF Orientation metadata.
- Gallery apps read this EXIF tag and display the photo correctly.
- labelImg and some other tools ignore the EXIF Orientation, showing the raw pixel data instead.
- As a result, photos may appear rotated when opened in labelImg.
Solution: Normalize the images before labeling by applying the EXIF Orientation to the pixel data and saving the corrected version. This can be done with tools like PIL (Python Imaging Library), exiftool, or ImageMagick.

```python
from PIL import Image, ExifTags
import os

def fix_orientation(img_path):
    img = Image.open(img_path)
    try:
        # EXIF Orientation
        for orientation in ExifTags.TAGS.keys():
            if ExifTags.TAGS[orientation] == 'Orientation':
                break
        
        exif = dict(img._getexif().items())

        if exif[orientation] == 3:
            img = img.rotate(180, expand=True)
        elif exif[orientation] == 6:
            img = img.rotate(270, expand=True)
        elif exif[orientation] == 8:
            img = img.rotate(90, expand=True)

        img.save(img_path) # rewrite
        print(f"fixed completed : {img_path}")
    except (AttributeError, KeyError, IndexError):
        # No EXIF Info, just pass
        pass

# All the picture in specific folder.
path = r'Your Path'
for filename in os.listdir(path):
    if filename.lower().endswith(('.jpg', '.jpeg', '.png')):
        fix_orientation(os.path.join(path, filename))
```


## Augmentation

```python
import cv2
import os
import albumentations as A
import glob

# 1. Augmentation Definition
# (Noise - C)
transform_c = A.Compose([
    A.GaussNoise(var_limit=(50.0, 150.0), p=1.0),
    A.MultiplicativeNoise(multiplier=(0.8, 1.2), p=1.0)
])

# (Rotate - R)
transform_r = A.Compose([
    A.Rotate(limit=45, p=1.0, border_mode=cv2.BORDER_CONSTANT, value=0)
])

# (Scale/Resize - S)
# Scale 0.5~1.5 times,  and fit to 640x640.
transform_s = A.Compose([
    A.RandomScale(scale_limit=0.5, p=1.0),
    A.Resize(width=640, height=640, p=1.0)
])

def run_augmentation(target_dir):
    # supported format
    extensions = ['*.jpg', '*.jpeg', '*.png']
    image_files = []
    for ext in extensions:
        image_files.extend(glob.glob(os.path.join(target_dir, ext)))

    print(f"Total {len(image_files)} files found.")

    for img_path in image_files:
        # split name (eg: AAA.jpg -> AAA)
        base_name = os.path.splitext(os.path.basename(img_path))[0]
        image = cv2.imread(img_path)
        image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

        # applied method just 1
        aug_list = [
            ('C', transform_c),
            ('R', transform_r),
            ('S', transform_s)
        ]

        for suffix, trans in aug_list:
            augmented = trans(image=image)['image']
            save_name = f"{base_name}_{suffix}_01.jpg"
            save_path = os.path.join(target_dir, save_name)
            
            # save (RGB -> BGR transform)
            cv2.imwrite(save_path, cv2.cvtColor(augmented, cv2.COLOR_RGB2BGR))
            print(f"saved : {save_name}")

if __name__ == "__main__":
    # modify your folder
    target_folder = "./your_dataset_path" 
    run_augmentation(target_folder)

```

## labelImg
⌨️ Essential Navigation Shortcuts
- D : Move to the Next image
- A : Move to the Previous image
💡 Extra Shortcuts to Speed Up Labeling
- W : Start Create RectBox mode (draw a bounding box)
- Ctrl + S : Save — images are usually auto‑saved when moving forward, but manual saving is safer
- Del : Delete the selected bounding box
- Ctrl + + / - : Zoom in / Zoom out on the image

## YOLO
These can be adjusted in the training configuration file (hyp.yaml) or as arguments in model.train().
- Mosaic (0.0–1.0): Combines 4 random images into one. Very effective for improving detection performance, especially for small objects. (Default usually 1.0)
- Mixup (0.0–1.0): Transparently overlays two images to create new training data.
- Degrees (0.0–180): Randomly rotates the image.
- Translate (0.0–1.0): Shifts the image up, down, left, or right.
- Scale (0.0–1.0): Zooms in or out on the image.
- Flipud / Fliplr (0.0–1.0): Flips the image vertically or horizontally. (Be cautious with horizontal flips for objects like traffic signs.)
- HSV_h / HSV_s / HSV_v: Adjusts Hue, Saturation, and Value to simulate lighting variations.

```python
from ultralytics import YOLO

model = YOLO('yolov8n.pt')  # Load model

# Set augmentation options at training start
model.train(
    data='data.yaml',
    epochs=100,
    imgsz=640,
    # Augmentation-related options below
    mosaic=1.0,      # Enable mosaic augmentation (highly recommended)
    degrees=15.0,    # Random rotation within ±15 degrees
    flipud=0.0,      # Vertical flip disabled (traffic signs must not be upside down)
    fliplr=0.5,      # Horizontal flip (recommended only if signs have no text)
    blur=0.1,        # Apply slight blur effect
    mixup=0.2        # Mix two images together
)
```


