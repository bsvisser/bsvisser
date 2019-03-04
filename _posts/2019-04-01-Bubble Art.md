---
layout: post
title: "Bubble art"
author: "Brent Visser"
categories: facts, all
tags: [bio]
image: max.png
---

```python
from PIL import Image, ImageDraw, ImageOps, ImageEnhance
import os
colors = []
list = []
current_directory = os.getcwd()
final_directory = os.path.join(current_directory, 'temp')
if not os.path.exists(final_directory):
    os.makedirs(final_directory)
    
    
    
def circles2(whatt,n):
    whatt = Image.open(whatt)
    xz,yz =  whatt.size
    xz,yz = xz*10, yz*10
    x1, y1 =  whatt.size
    x1,y1 = x1*10,y1*10
    size =xz,yz
    
    mask = Image.new('L', size, 0)
    draw = ImageDraw.Draw(mask) 
    draw.ellipse([0,0, x1, y1], fill='white')
    #im = Image.open('image.jpg')
    mask = mask.resize(whatt.size, Image.ANTIALIAS)
    output = ImageOps.fit(whatt, mask.size, centering=(0.5, 0.5))
    output.putalpha(mask)
    return output
    

def average_color(file, n, x,y):
    #i = Image.open(filename, 'r').load()
    i=file.load()
    r, g, b = 0, 0, 0
    count = 0
    for s in range(x, x+n):
        for t in range(y, y+n):
            pixlr, pixlg, pixlb = i[s, t]
            r += pixlr
            g += pixlg
            b += pixlb
            count += 1
    return (int((r/count)), int((g/count)), int((b/count)))






def do_it_all(filename, n):
    i = Image.open(filename, 'r')
    width, height = i.size
    nwidth, nheight = int((width)/n), int((height)/n)
    ntimes = nwidth * nheight
    #i=i.crop((0,0,900,700))
    x,y = 0,0
    times=0
    ya=0
    while times < ntimes:
        r, g, b = average_color(i, n, x,y)
        os.chdir(final_directory)
        colors = ((r,g,b),) *n*n
        im2 = Image.new('RGB', (n, n))
        im2.putdata(colors)
        im2.save("imx.png")
        im3=circles2("imx.png",n)
        while os.path.exists("im%s.png" % ya):
            ya += 1
        im3.save("im%s.png" % ya)
        list.append("im%s.png" % ya)
        os.remove("imx.png")
        times += 1
        x += n
        if x >= width:
            x = 0
            y += n
        if y >= height:
            y = height-n
        os.chdir(current_directory)
    os.chdir(final_directory)
    images = [ Image.open(i) for i in list ]
    new_im = Image.new('RGBA', (width, height))   
    x_offset = 0
    y_offset = 0
    for im in images:
        new_im.paste(im, (x_offset,y_offset))
        x_offset += n
        if x_offset >= width:
            x_offset = 0
            y_offset += n
        if y_offset >= height:
            y_offset = height-n
    files = os.listdir(final_directory)
    files = os.listdir(final_directory)
    for file in files:
        if file.endswith(".png"):
            os.remove(os.path.join(final_directory, file))
    os.chdir(current_directory)
    os.rmdir(final_directory)
    new_im.save('test2.png')
    im=Image.open("test2.png")
    bg_colour=(255, 255, 255)
    alpha = im.convert('RGBA').split()[-1]

        # Create a new background image of our matt color.
        # Must be RGBA because paste requires both images have the same format
        # (http://stackoverflow.com/a/8720632  and  http://stackoverflow.com/a/9459208)
    bg = Image.new("RGBA", im.size, bg_colour + (255,))
    bg.paste(im, mask=alpha)
    bg.convert('RGB')
    z= ImageEnhance.Contrast(bg).enhance(1.25)
    z.save("max2.png")
    a=bg.convert('L')
    a= ImageEnhance.Contrast(a).enhance(1.5)
    a.save("max3.png")
    

    


do_it_all('bernie-sanders21.png', 20)
#print(r,g,b)
```