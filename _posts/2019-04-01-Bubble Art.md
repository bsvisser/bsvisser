---
layout: post
title: "Bubble art"
author: "Brent Visser"
categories: facts, all
tags: [bio]
image: max.png
---

```python
from PIL import ImageOps, Image, ImageDraw

def circles(what):
    img11 = Image.open(what)
    back = Image.new('RGBA', img11.size)
    back.paste(img11)
    poly = Image.new('RGBA', (512,512))
    x1, y1 =  img11.size
    draw = ImageDraw.Draw(poly)
    draw.ellipse([1,1, x1-1, y1-1], fill='white')

    if poly.mode == 'RGBA':
        r,g,b,a = poly.split()
        rgb_image = Image.merge('RGB', (r,g,b))

        inverted_image = ImageOps.invert(rgb_image)
        inverted_image.show()
        r2,g2,b2 = inverted_image.split()

        final_transparent_image = Image.merge('RGBA', (r2,g2,b2,a))

    else:
        inverted_image = ImageOps.invert(poly)

    back.paste(poly, (0,0), mask=final_transparent_image)
    back.show()
    
def circles2(whatt):
    whatt = Image.open(whatt)
    size =  whatt.size
    x1, y1 =  whatt.size
    mask = Image.new('L', size, 0)
    draw = ImageDraw.Draw(mask) 
    draw.ellipse([1,1, x1-1, y1-1], fill='white')
    #im = Image.open('image.jpg')
    output = ImageOps.fit(whatt, mask.size, centering=(0.5, 0.5))
    output.putalpha(mask)
    datas = output.getdata()
    print(datas)
    newData = []
    for item in datas:
        if item[3] == 0:
            newData.append((255, 255, 255, 255))
        else:
            newData.append(item)
    output.putdata(newData)
    output.save("test.png", "PNG") 
    
    
circles2("testya.png")
```