---
layout:       post
title:        "模型的可视化代码,开源软件"
author:       "TechShrimp"
header-style: text
catalog:      true
tags:
    - Web
    - JavaScript
---



> 雪碧（doodlewind）邀请我给[《JavaScript 二十年》](https://zhuanlan.zhihu.com/p/373065151) 写的推荐序。

JavaScript 常常被戏称为一门偶然成功的玩具语言。而实际上，它出身名门，更是成长在聚光灯之下。纵观历史，有资格被标准化的编程语言甚少，它因此成为多方角力的战场，却也有幸同时得到业界与学界先驱的亲传。时至今日，我们甚至难言是它背负了太多妥协，还是这些妥协才成就了它呢。以史为鉴，或许你会有自己的答案。

— 黄玄，Facebook 软件工程师（编程语言、JS 引擎、前端基础设施）、中文前端社区活跃成员。



### 第一种：模型的可视化

```python

pip install netron 



```

### 第二种：特征图的可视化

```python

import time
import matplotlib.pyplot as plt
import numpy as np


def draw_features(width,height,x,savename):
    tic=time.time()
    fig = plt.figure(figsize=(16, 16))
    fig.subplots_adjust(left=0.05, right=0.95, bottom=0.05, top=0.95, wspace=0.05, hspace=0.05)
    for i in range(width*height):
        plt.subplot(height,width, i + 1)
        plt.axis('off')
        # plt.tight_layout()
        img = x[0, i, :, :]
        pmin = np.min(img)
        pmax = np.max(img)
        img = (img - pmin) / (pmax - pmin + 0.000001)
        plt.imshow(img, cmap='gray')
        print("{}/{}".format(i,width*height))
    fig.savefig(savename, dpi=100)
    fig.clf()
    plt.close()
    print("time:{}".format(time.time()-tic))



---------------------forward这里--------------
draw_features(8, 8, x.cpu().detach().numpy(), "{}/f1_conv1.png".format(self.savepath))  
print("{}/f1_conv1.png".format(self.savepath))

```

### 第三种：热度图可视化【要用到grad-cam这个包】

```python
第一步也是安装包
pip install grad-cam

代码：
import numpy as np
import cv2

import torchvision.models as models
import torchvision.transforms as transforms

import pytorch_grad_cam
from pytorch_grad_cam.utils.image import show_cam_on_image



resnet18 = models.resnet18(pretrained=True)  
resnet18.eval() 
target_layers = [resnet18.layer4[1].bn2]  


origin_img = cv2.imread('./img.png')  
rgb_img = cv2.cvtColor(origin_img, cv2.COLOR_BGR2RGB) 


trans = transforms.Compose([
    transforms.ToTensor(),  
    transforms.Resize(224),  
    transforms.CenterCrop(224) 
])
crop_img = trans(rgb_img)  
net_input = transforms.Normalize((0.485, 0.456, 0.406), (0.229, 0.224, 0.225))(crop_img).unsqueeze(0)  


canvas_img = (crop_img * 255).byte().numpy().transpose(1, 2, 0)
canvas_img = cv2.cvtColor(canvas_img, cv2.COLOR_RGB2BGR) 


cam = pytorch_grad_cam.GradCAMPlusPlus(model=resnet18, target_layers=target_layers)  
grayscale_cam = cam(net_input)  
grayscale_cam = grayscale_cam[0, :]  


src_img = np.float32(canvas_img) / 255  
visualization_img = show_cam_on_image(src_img, grayscale_cam, use_rgb=False)  
cv2.imshow('feature map', visualization_img) 
cv2.waitKey(0)  
```

