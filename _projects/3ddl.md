---
title: "3DDL"
excerpt: "An end-to-end object-to-model computer vision solution"
header:
  teaser: /assets/images/projects/3ddl_concept.png
classes: wide
gallery:
  - url: /assets/images/projects/3ddl_concept.png
    image_path: /assets/images/projects/3ddl_concept.png
    alt: "3ddl concept"
  - url: /assets/images/projects/3ddlapp.png
    image_path: /assets/images/projects/3ddlapp.png
    alt: "3ddl app"
---

Link to github project page: [here](https://github.com/whong92/3D_DL)

This is a project I worked on when I was pursuing a Masters in Computer Science at Imperial College London. This project was undertaken as an industrial project required to fulfill our degree.

The problem we had was to train a reliable classifier for grocery products for [Ocado Technology](https://www.ocadogroup.com/technology/technology-pioneers), without much reliable data. Most of the images we had were from very generic views online, or some poorly taken images from a warehouse facility.

We managed to bring together a solution that takes in an actual instance of a grocery product, scan the product and generate a 3D model, and use a graphics engine to generate an unlimited amount of training images. We the trained an object classifier and an object detector, and developed a mobile app that we could use to help people re-order a grocery product from the Ocado website from an image of the product.

{% include gallery caption="(Left) the workflow for 3D-DL, (Right) a grocery product recognition app made using 3D-DL." %}

The technologies we used included photogrammetry ([3DF Zephyr](https://www.3dflow.net/)), graphics processing ([Blender](https://www.blender.org/)), and various deep learning libraries ([Tensorflow](https://www.tensorflow.org/), [Keras](https://keras.io/)).