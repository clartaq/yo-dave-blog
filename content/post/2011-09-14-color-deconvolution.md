---
title: Color Deconvolution
tags:
  - mathematica
id: 446
comment: false
categories:
  - programming
date: 2011-09-14 20:04:19
modified: 2018-03-25T15:43:16.693783-04:00
---

One method I've used in a number of imaging tasks is Color Deconvolution. You can think of color deconvolution as figuring out how much of each of a group of colors contributes to the final color of the pixels in an image.

<!--more-->
In my previous work, I've used this technique to analyze and quantify the amount of dye in a histologically stained tissue sample. For example, you may want to know how much eosin (a bright pinkish-red dye) and hematoxylin (purplish-blue dye) are present in a region of stained tissue.

Here's a magnified image of a piece of tissue stained with hematoxylin and eosin. Those are red blood cells near the top. The dark purple structures near the bottom are nuclei. The rest of the pink stuff is stained mostly with eosin.

[![Example of H&E Stained Tissue](/static/img/2011-09-14-HnEImage.jpg "Example of H&E Stained Tissue") <br><small>Example of H&E Stained Tissue</small>](/static/img/2011-09-14-HnEImage.jpg)

What if you want to know how much of each dye is staining a particular structure. Color deconvolution is the technique to use to find out. In addition to a big pile of numbers, the output can be shown visually to give an impression of the relative intensities. Below is an example of such output

[![Three Channels of Deconvolved Image](/static/img/2011-09-14-DeconvSnip.PNG "Three Channels of Deconvolved Image")<br><small>Three Channels of Deconvolved Image</small>](/static/img/2011-09-14-DeconvSnip.PNG)

The left sub-image represents the intensity of hematoxylin and is shown in roughly the color of the dye itself. The middle image shows how eosin is distributed through the tissue. The right image is what's "left over", neither hematoxylin or eosin.

There are a number of ways to do these calculations. The canonical way is the method first presented by Ruifrok and Johnston in their 2001 paper "Quantification of Histochemical Staining by Color Deconvolution". This is a supervised method where the user must provide a color basis for the dyes based on a manual calibration process.

A more helpful method might be some unsupervised calculation such as that provided by Macenko _et al_. in their 2009 paper "A Method for Normalizing Histology Slides for Quantitative Analysis". However, I haven't found any unsupervised method that reliably gives results as good as a supervised method.

I've put some of my work on the implementation of these calculations up in a BitBucket respository at [https://bitbucket.org/David_Clark/color-deconvolution](https://bitbucket.org/David_Clark/color-deconvolution). The files there consist of annotated worked examples and code in [_Mathematica_](http://www.wolfram.com/mathematica/) notebooks. The original papers referred to here are also available in the repository. To alter the files you will need a full copy of _Mathematica_ 8\. If you would just like to view them, you can use the free [_Mathematica_ CDF Player](http://www.wolfram.com/products/player/).
