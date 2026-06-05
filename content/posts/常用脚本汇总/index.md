---
title: "常用脚本汇总"
date: 2022-04-22T02:44:42.000Z
draft: false
tags: ["DIY"]
---
> 图片PNG文件转JPG文件；

**使用方法：将该脚本放置于png文件所在的文件夹中，然后运行该脚本，即可将png文件转化为jpg文件，并放置于JPG文件夹下；**

```python
import os
from PIL import Image
dirname_read = "./"  								# 源文件文件夹
dirname_write = "./JPG/"  							# 目标文件文件夹
names = os.listdir(dirname_read)
count = 0
for name in names:
    portion = os.path.splitext(name)  				# 分离文件名和扩展名
    if portion[1] == ".png":          				# 判断扩展名是否为png
        img = Image.open(dirname_read+name) 		# 打开该文件
        name = portion[0] + ".jpg" 					# 重命名文件
        to_save_path = dirname_write + name 		# 设置保存路径
        img = img.convert('RGB')
        # RGBA意思是红色，绿色，蓝色,Alpha指透明度。而JPG不支持透明度，所以要么丢弃Alpha,要么保存为.png文件
        img.save(to_save_path, quality=95)  		# 保存
        count += 1  								# 计数加一
        print(to_save_path, "------conut:", count)  # 输出信息
    else:
        continue
print("Count_Sum:", count)  						# 输出总数
```
