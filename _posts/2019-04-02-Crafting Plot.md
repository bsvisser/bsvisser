---
layout: post
title: "Crafting methods visualized"
author: "Brent Visser"
categories: facts, all
tags: [bio]
image: crafting.png
---

<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~bsvisser/11.embed" height="525" width="100%"></iframe>


```python
# -*- coding: utf-8 -*-
"""
Created on Fri Feb 22 14:55:55 2019

@author: Brent Visser
"""
from bs4 import BeautifulSoup
import csv
import pandas as pd
from urllib.request import urlopen
import numpy as np
import os
import locale
locale.setlocale(locale.LC_NUMERIC, '')
import plotly as plotly
import plotly.plotly as py
import plotly.graph_objs as go
import plotly.io as pio
from plotly.offline import iplot, init_notebook_mode
import plotly.graph_objs as go
from subprocess import call
import json
html = urlopen("https://oldschoolrunescape.fandom.com/wiki/Calculator:Crafting/Tanned_hides").read()
bs = BeautifulSoup(html, 'html.parser')
#bs.prettify()

if os.path.exists("my_file.csv"):
    os.remove("my_file.csv")

tables = bs.findAll("table")
#print(bs)
tables.pop(0)
for mytable in tables:
    for trs in mytable.find_all('tr'):
        tds = trs.find_all('td')
        row = [elem.text.strip() for elem in tds]
        row= filter(lambda a: a != '"', row)
        #print(row)
        with open('my_file.csv', 'a') as f:
            writer = csv.writer(f, dialect='excel',delimiter=';')
            writer.writerow(row)
            
            
x = []
y = []
df = pd.DataFrame()

with open("my_file.csv", 'r') as f:
    for line in f:
        df = pd.concat( [df, pd.DataFrame([tuple(line.strip().split(';'))])], ignore_index=True )

df=df.drop(columns=3)
df=df.dropna(subset=[1])
#print(df[6])
df = df.apply(lambda x: x.str.strip() if isinstance(x, str) else x).replace('', np.nan)
df[6] = df[6].str.replace(',','').astype(float)
df[2] = df[2].astype(float)
df[1] = df[1].str.replace(',','').astype(float)

df['Descrip Level'] = [
    ' '.join([(x), '- Level:', str(y)]) for x, y in zip(df[0], df[1])]


#ax1 = df.plot.scatter(x=2,y=6,c=1, colormap = "Oranges")





cols      = df[1]
#df['test'] = df[[0, 1]].apply(lambda x: ''.join(x), axis=1)
# Create a trace
trace =go.Scatter(
    x = df[2],
    y = df[6],
    name= 'Hides',
    text=df['Descrip Level'],
    marker=dict(
        
        size=20,
        cmax=99,
        cmin=0,
        opacity = 0.8,
        color=df[1],
        colorbar=dict(
            title='Level requirement'),
            colorscale='Portland'),
        mode='markers',
        )
########################################################################
html = urlopen("https://oldschoolrunescape.fandom.com/wiki/Calculator:Crafting/Jewellery").read()
bs = BeautifulSoup(html, 'html.parser')
#bs.prettify()

if os.path.exists("my_file.csv"):
    os.remove("my_file.csv")

tables = bs.findAll("table")


#print(bs)
tables.pop(0)
jewelrytype = []
jewelrytypef = []

for link in tables:
    jewelrytype.append(link.find('a')['href'])

jewelrytype = ' '.join(jewelrytype).replace('/wiki/','').split()
for i in jewelrytype:
    jewelrytypef.append(i.split('_')[0])

jewelrytype=np.repeat(jewelrytypef, 4)
#print(jewelrytype)
for mytable in tables:
    for trs in mytable.find_all('tr'):
        tds = trs.find_all('th')
        row = [elem.text.strip() for elem in tds]
        row= filter(lambda a: a != '"', row)
        #print(row)
        with open('my_file.csv', 'a') as f:
            writer = csv.writer(f, dialect='excel',delimiter=';')
            writer.writerow(row)
            

            
x = []
y = []
df = pd.DataFrame()
se = pd.Series(jewelrytype)
remove_words = ['Total']

with open("my_file.csv", 'r') as f:
    for line in f:
        df = pd.concat( [df, pd.DataFrame([tuple(line.strip().split(';'))])], ignore_index=True )
        
df=df.dropna(subset=[1])

mask = np.column_stack([df[col].astype(str).str.contains(r"Level", na=False) for col in df])
df2 = df.loc[~mask.any(axis=1)]
df = df2
df=df.drop(columns=3)
df = df.apply(lambda x: x.str.strip() if isinstance(x, str) else x).replace('', np.nan)
df['Type'] = se.values
df['Types'] = [
    ' '.join([str(x), '', y]) for x, y in zip(df['Type'], df[0])]

df['Descrip Level Type'] = [
    ' '.join([(x), '- Level:', str(y)]) for x, y in zip(df['Types'], df[1])]

########################################################################
#Kick Zenyte
df=df[df.Type != 'Zenyte']
#print(df['Type'])




df[6] = df[6].str.replace(',','').astype(float)
df[8] = df[8].str.replace(',','').astype(float)
df[1] = df[1].str.replace(',','').astype(float)

#ax1 = df.plot.scatter(x=2,y=6,c=1, colormap = "Oranges")





cols      = df[1]



# Create a trace
trace1 =go.Scatter(
    x = df[7],
    y = df[8],
    name= 'Jewellery',
    text=df['Descrip Level Type'],
    marker=dict(
        
        size=17,
        cmax=99,
        cmin=0,
        opacity = 0.8,
        color=df[1],
        colorbar=dict(
            title='Level requirement'),
            colorscale='Portland'),
        mode='markers',
        )
        
#########################################################
html = urlopen("https://oldschoolrunescape.fandom.com/wiki/Calculator:Crafting/Gem_cutting").read()
bs = BeautifulSoup(html, 'html.parser')
#bs.prettify()

if os.path.exists("my_file.csv"):
    os.remove("my_file.csv")

tables = bs.findAll("table")
#print(bs)
tables.pop(0)
for mytable in tables:
    for trs in mytable.find_all('tr'):
        tds = trs.find_all('th')
        row = [elem.text.strip() for elem in tds]
        row= filter(lambda a: a != '"', row)
        #print(row)
        with open('my_file.csv', 'a') as f:
            writer = csv.writer(f, dialect='excel',delimiter=';')
            writer.writerow(row)
            
input_file = open('my_file.csv', 'r')
output_file = open('my_file2.csv', 'w')
dat = csv.reader(input_file)
writer = csv.writer(output_file, dialect='excel')
specials = '*'

for line in dat:
    line = [value.replace(specials, '') for value in line]
    writer.writerow(line)

input_file.close()
output_file.close()
    
x = []
y = []
df = pd.DataFrame()

with open("my_file2.csv", 'r') as f:
    for line in f:
        df = pd.concat( [df, pd.DataFrame([tuple(line.strip().split(';'))])], ignore_index=True )
#print(df)
mask = np.column_stack([df[col].astype(str).str.contains(r"Level", na=False) for col in df])
df2 = df.loc[~mask.any(axis=1)]
df = df2
df=df.dropna(subset=[1])
df = df.apply(lambda x: x.str.strip() if isinstance(x, str) else x).replace('', np.nan)
df[4] = df[4].str.replace(',','').astype(float)
df[6] = df[6].astype(float)
df[1] = df[1].str.replace(',','').astype(float)
#Kick Zenyte
df=df[df[0] != 'Zenyte']
df['Descrip Level'] = [
    ' '.join([(x), '- Level:', str(y)]) for x, y in zip(df[0], df[1])]


cols      = df[1]

trace2 =go.Scatter(
    x = df[6],
    y = df[4],
    name= 'Cutting gems',
    text=df['Descrip Level'],
    marker=dict(
        
        size=20,
        cmax=99,
        cmin=0,
        opacity = 0.7,
        color=df[1],
        colorbar=dict(
            title='Level requirement'),
            colorscale='Portland'),
        mode='markers',
        )

###################################################################
        
html = urlopen("https://oldschoolrunescape.fandom.com/wiki/Calculator:Crafting/Battlestaves").read()
bs = BeautifulSoup(html, 'html.parser')
#bs.prettify()

if os.path.isfile("my_file.csv"):
    os.remove("my_file.csv")
if os.path.isfile("my_file2.csv"):
    os.remove("my_file2.csv")

tables = bs.findAll("table")
#print(bs)
i=0
tables.pop(0)
tables.pop(0)
while i < 3:
    tables.pop(1)
    i+=1
#print(tables)
for mytable in tables:
    for trs in mytable.find_all('tr'):
        tds = trs.find_all('td')
        row = [elem.text.strip() for elem in tds]
        row= filter(lambda a: a != '"', row)
        #print(row)
        with open('my_file.csv', 'a') as f:
            writer = csv.writer(f, dialect='excel',delimiter=';')
            writer.writerow(row)
            
input_file = open('my_file.csv', 'r')
output_file = open('my_file2.csv', 'w')
dat = csv.reader(input_file)
writer = csv.writer(output_file, dialect='excel')
specials = '*'

for line in dat:
    line = [value.replace(specials, '') for value in line]
    writer.writerow(line)

input_file.close()
output_file.close()
    
x = []
y = []
df = pd.DataFrame()

with open("my_file2.csv", 'r') as f:
    for line in f:
        df = pd.concat( [df, pd.DataFrame([tuple(line.strip().split(';'))])], ignore_index=True )

mask = np.column_stack([df[col].astype(str).str.contains(r"Level", na=False) for col in df])
df2 = df.loc[~mask.any(axis=1)]
df = df2
df=df.dropna(subset=[1])
#print(df)
df = df.apply(lambda x: x.str.strip() if isinstance(x, str) else x).replace('', np.nan)
df[2] = df[2].str.replace(',','').astype(float)
df[1] = df[1].astype(float)
df[5] = df[5].str.replace(',','').astype(float)
#Kick Zenyte
df=df[df[0] != 'Zenyte']
df['Descrip Level'] = [
    ' '.join([(x), '- Level:', str(y)]) for x, y in zip(df[0], df[1])]


cols      = df[1]

trace3 =go.Scatter(
    x = df[2],
    y = df[5],
    name= 'Unpowered orbs and battlestaves (GE)',
    text=df['Descrip Level'],
    marker=dict(
        
        size=20,
        cmax=99,
        cmin=0,
        opacity = 0.7,
        color=df[1],
        colorbar=dict(
            title='Level requirement'),
            colorscale='Portland'),
        mode='markers',
        )
        
    ##################################################################################

html = urlopen("https://oldschoolrunescape.fandom.com/wiki/Calculator:Crafting/Battlestaves").read()
bs = BeautifulSoup(html, 'html.parser')
#bs.prettify()

if os.path.isfile("my_file.csv"):
    os.remove("my_file.csv")
if os.path.isfile("my_file2.csv"):
    os.remove("my_file2.csv")

tables = bs.findAll("table")
#print(bs)
i = 0
while i < 4:
    i+=1
    tables.pop(0)
tables.pop(1)

#print(tables)
for mytable in tables:
    for trs in mytable.find_all('tr'):
        tds = trs.find_all('td')
        row = [elem.text.strip() for elem in tds]
        row= filter(lambda a: a != '"', row)
        #print(row)
        with open('my_file.csv', 'a') as f:
            writer = csv.writer(f, dialect='excel',delimiter=';')
            writer.writerow(row)
            
input_file = open('my_file.csv', 'r')
output_file = open('my_file2.csv', 'w')
dat = csv.reader(input_file)
writer = csv.writer(output_file, dialect='excel')
specials = '*'

for line in dat:
    line = [value.replace(specials, '') for value in line]
    writer.writerow(line)

input_file.close()
output_file.close()
    
x = []
y = []
df = pd.DataFrame()

with open("my_file2.csv", 'r') as f:
    for line in f:
        df = pd.concat( [df, pd.DataFrame([tuple(line.strip().split(';'))])], ignore_index=True )

mask = np.column_stack([df[col].astype(str).str.contains(r"Level", na=False) for col in df])
df2 = df.loc[~mask.any(axis=1)]
df = df2
df=df.dropna(subset=[1])
#print(df)
df = df.apply(lambda x: x.str.strip() if isinstance(x, str) else x).replace('', np.nan)
df[2] = df[2].str.replace(',','').astype(float)
df[1] = df[1].astype(float)
df[5] = df[5].str.replace(',','').astype(float)

#Kick Zenyte
df=df[df[0] != 'Zenyte']
df['Descrip Level'] = [
    ' '.join([(x), '- Level:', str(y)]) for x, y in zip(df[0], df[1])]


cols      = df[1]

trace4 =go.Scatter(
    x = df[2],
    y = df[5],
    name= 'Powered orbs and battlestaves (GE)',
    text=df['Descrip Level'],
    marker=dict(
        
        size=20,
        cmax=99,
        cmin=0,
        opacity = 0.7,
        color=df[1],
        colorbar=dict(
            title='Level requirement'),
            colorscale='Portland'),
        mode='markers',
        )
        
####################################################################


html = urlopen("https://oldschoolrunescape.fandom.com/wiki/Calculator:Crafting/Glass").read()
bs = BeautifulSoup(html, 'html.parser')
#bs.prettify()

if os.path.isfile("my_file.csv"):
    os.remove("my_file.csv")
if os.path.isfile("my_file2.csv"):
    os.remove("my_file2.csv")

tables = bs.findAll("table")
#print(bs)

tables.pop(0)
for mytable in tables:
    for trs in mytable.find_all('tr'):
        tds = trs.find_all('td')
        row = [elem.text.strip() for elem in tds]
        row= filter(lambda a: a != '"', row)
        #print(row)
        with open('my_file.csv', 'a') as f:
            writer = csv.writer(f, dialect='excel',delimiter=';')
            writer.writerow(row)
            
input_file = open('my_file.csv', 'r')
output_file = open('my_file2.csv', 'w')
dat = csv.reader(input_file)
writer = csv.writer(output_file, dialect='excel')
specials = '*'

for line in dat:
    line = [value.replace(specials, '') for value in line]
    writer.writerow(line)
    

input_file.close()
output_file.close()
    
x = []
y = []
df = pd.DataFrame()
with open("my_file2.csv", 'r') as f:
    for line in f:
        df = pd.concat( [df, pd.DataFrame([tuple(line.strip().split(';'))])], ignore_index=True )
        
df = df.drop(df.columns[[0]], axis=1)  # df.columns is zero-based pd.Index 

mask = np.column_stack([df[col].astype(str).str.contains(r"Level", na=False) for col in df])
df2 = df.loc[~mask.any(axis=1)]
df = df2
df=df.dropna(subset=[1])
df = df.apply(lambda x: x.str.strip() if isinstance(x, str) else x).replace('', np.nan)
df[3] = df[3].str.replace(',','').astype(float)
df[2] = df[2].astype(float)
df[6] = df[6].str.replace(',','').astype(float)

#Kick Zenyte
df['Descrip Level'] = [
    ' '.join([(x), '- Level:', str(y)]) for x, y in zip(df[1], df[2])]

cols      = df[1]

trace5 =go.Scatter(
    x = df[3],
    y = df[6],
    name= 'Glassblowing (buying molten glass)',
    text=df['Descrip Level'],
    marker=dict(
        
        size=20,
        cmax=99,
        cmin=0,
        opacity = 0.7,
        color=df[2],
        colorbar=dict(
            title='Level requirement'),
            colorscale='Portland'),
        mode='markers',
        )
###########################################################
html = urlopen("https://oldschoolrunescape.fandom.com/wiki/Calculator:Crafting/Magic_armour").read()
bs = BeautifulSoup(html, 'html.parser')
#bs.prettify()

if os.path.isfile("my_file.csv"):
    os.remove("my_file.csv")
if os.path.isfile("my_file2.csv"):
    os.remove("my_file2.csv")

tables = bs.findAll("table")

#print(tables)
for mytable in tables:
    for trs in mytable.find_all('tr'):
        tds = trs.find_all('td')
        row = [elem.text.strip() for elem in tds]
        row= filter(lambda a: a != '"', row)
        #print(row)
        with open('my_file.csv', 'a') as f:
            writer = csv.writer(f, dialect='excel',delimiter=';')
            writer.writerow(row)
            
input_file = open('my_file.csv', 'r')
output_file = open('my_file2.csv', 'w')
dat = csv.reader(input_file)
writer = csv.writer(output_file, dialect='excel')
specials = '*'

for line in dat:
    line = [value.replace(specials, '') for value in line]
    writer.writerow(line)

input_file.close()
output_file.close()
    
x = []
y = []
df = pd.DataFrame()

with open("my_file2.csv", 'r') as f:
    for line in f:
        df = pd.concat( [df, pd.DataFrame([tuple(line.strip().split(';'))])], ignore_index=True )

mask = np.column_stack([df[col].astype(str).str.contains(r"Level", na=False) for col in df])
df2 = df.loc[~mask.any(axis=1)]
df = df2
df=df.dropna(subset=[1])
#print(df)
df = df.apply(lambda x: x.str.strip() if isinstance(x, str) else x).replace('', np.nan)
df[2] = df[2].str.replace(',','').astype(float)
df[1] = df[1].astype(float)
df[6] = df[6].str.replace(',','').astype(float)
#Kick Zenyte
df=df[df[0] != 'Zenyte']
df['Descrip Level'] = [
    ' '.join([(x), '- Level:', str(y)]) for x, y in zip(df[0], df[1])]


cols      = df[1]

trace6 =go.Scatter(
    x = df[2],
    y = df[6],
    name= 'Xerician Fabric',
    text=df['Descrip Level'],
    marker=dict(
        
        size=20,
        cmax=99,
        cmin=0,
        opacity = 0.7,
        color=df[1],
        colorbar=dict(
            title='Level requirement'),
            colorscale='Portland'),
        mode='markers',
        )
        
data = [trace, trace1, trace2, trace3,trace4,trace5,trace6]

layout = go.Layout(
    title='Crafting XP vs GP (filtered out zenytes)',
    hovermode ='closest',
    autosize=False,
    legend=dict(x=-.1, y=1.2),
    width=1400,
    height=800,
    xaxis=dict(
        autorange=True,
        showgrid=True,
        zeroline=True,
        showline=True,
        ticks='inside',
        ticksuffix= " XP",
        showticklabels=True,
                title='XP gained',
        titlefont=dict(
            family='Verdana, sans-serif',
            size=20,
            color='grey')
    ),
    yaxis=dict(
        autorange=True,
        showgrid=True,
        zeroline=True,
        showline=False,
        ticks='',
        ticksuffix= " GP",
        showticklabels=True,
                title='GP Profit/Loss',
        titlefont=dict(
            family='Verdana, sans-serif',
            size=21,
            color='grey'
        ),
        )
    )
fig = go.Figure(data, layout=layout)
plot_url = py.plot(fig, filename='GPEXP')
if not os.path.exists('images'):
    os.mkdir('images')

pio.write_image(fig, 'images/fig1.png')




#storeTable = tables[1].find_all("tr")
#storeValueRows = tables[3].find_all("tr")
#storeRank = []
#for row in storeTable:
#    storeRank.append(row.get_text().strip())
#
#storeMatrix = []
#for row in storeValueRows:
#    storeMatrixRow = []
#    for cell in row.find_all("td")[::2]:
#        storeMatrixRow.append(cell.get_text().strip())
#    storeMatrix.append(", ".join(storeMatrixRow))
#
#for record in zip(storeRank, storeMatrix):
#    print (" ".join(record))
#table2 = bs.findAll("table")


#table = bs.find(lambda tag: tag.name=='table' and tag.has_attr('id') and tag['id']=="Table1") 
#rows = table.findAll(lambda tag: tag.name=='tr')
```