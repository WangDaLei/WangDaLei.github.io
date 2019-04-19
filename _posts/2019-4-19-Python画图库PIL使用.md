---
title: Python画图库PIL使用
published: true
---


## [](#header-3)图片的打开

*   从文件打开

```python
from PIL import Image
image = Image.open('./images/test.png')
image = Image.open('./images/test.png').convert("RGBA")
# 把图片装换成为RGBA模式, 如果操作多张图片最好统一成为一种格式
```

*   从Url网络文件打开

```python
import requests
from PIL import Image
from io import BytesIO
response = requests.get("https://www.baidu.com/img/bd_logo1.png?where=super")
image = Image.open(BytesIO(response.content))
```

## [](#header-3)给图片添加文字

```python
from PIL import Image, ImageDraw
image = Image.open('./images/test.png').convert("RGBA")
draw = ImageDraw.Draw(image)
font = ImageFont.truetype("./Lato-Medium.ttf", 38)
# 执行文章展示的格式，包括字体和大小，字体可以使用系统自带的文件，也可行下载一种字体文件，使用本地文件
draw.text((100, 96), "Hello World", (18, 18, 18), font=font)
# 第一个参数是坐上的像素点位置， 第二个位置展示的内容，需要上面选择的字体支持的文字才可以在图中正常展示，
# 第三个指定文章展示的颜色RGB值，第四个是格式对象
```

## [](#header-3)图片叠加和保存

```python
from PIL import Image, ImageDraw
image = Image.open('./images/test.png').convert("RGBA")
image2 = Image.open('./images/test2.png').convert("RGBA")
image.alpha_composite(image2, dest=(100, 100))
# 把第二个图像覆盖到第一个坐上像素点为100，100的位置

image_base.convert('RGB').save('./images/result.png', 'PNG')
# 以PNG的格式存储图片
```

## [](#header-3)图片压缩和生成圆图像

```python
from PIL import Image, ImageDraw
image = Image.open('./images/test.png').convert("RGBA")
width, height = image.size
r2 = min(width, height)
image = image.resize((r2, r2), Image.ANTIALIAS)
# 压缩图片成为正方形的图片
imb = Image.new('RGBA', (r2, r2), (255, 255, 255, 0))
pima = image.load()
pimb = imb.load()
r = float(r2 / 2)

# 取在圆内部的图片复制，其余为透明
for i in range(r2):
    for j in range(r2):
        lx = abs(i - r + 0.5)
        ly = abs(j - r + 0.5)
        l = pow(lx, 2) + pow(ly, 2)
        if l <= pow(r, 2):
            pimb[i, j] = pima[i, j]

# 这样imb就是原图像的圆图像
```


> **只是用到的一些函数的介绍**
>
> 详细参见[PIL文档](https://pillow.readthedocs.io/en/stable/)

* * *