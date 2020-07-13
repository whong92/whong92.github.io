---
title: "RecLibWH"
excerpt: "A Library of Recommender Algorithms using Matrix Factorization and Probabilistic Graphical Models"
header:
  teaser: /assets/images/projects/EgoBigLebowski.png
classes: wide
# sidebar:
#   - title: "Summary"
#     image: /assets/images/EgoBigLebowski.png
#     image_alt: "logo"
#     text: "Designer, Front-End Developer"
#   - title: "Responsibilities"
#     text: "Reuters try PR stupid commenters should isn't a business model"
gallery:
  - url: /assets/images/projects/EgoBigLebowski.png
    image_path: /assets/images/projects/EgoBigLebowski.png
    alt: "placeholder image 1"
  - url: /assets/images/projects/EgoGodFather.png
    image_path: assets/images/projects/EgoGodFather.png
    alt: "placeholder image 2"
---

Link to github project page: [here](https://github.com/whong92/recommender)

RecLibWH started off as a small side-project for making a custom recommender algorithm for my movie nights. Searching for the right movie is exhausting business! 

It has since turned into a project where I learn about various machine learning tecniques through the lens of movie recommenders. The purpose of RecLibWH is to have a library to make available all these different machine learning algorithms.

This project has seen me going from simple nearest-neighbour collaborative filtering techniques, to sophisticated probabilistic matrix factorization techniques. Next on the to-do list is the implementation of Bayesian techniques based on a variant of latent dirichlet allocation (LDA), a popular but powerful technique used in NLP.


I also made several apps based on this library, one of the cooler-looking ones are ego-graphs centered on a single user-chosen movie, with edges drawn between itself and closely related titles, as well as edges between those titles. The following images show ego graphs for "The Big Lebowski" and "The Godfather". (Click on them to get a better view)

{% include gallery caption="Some example Ego Graphs." %}

For more information on how these are generated, here is the [notebook](https://github.com/whong92/recommender/blob/master/notebooks/ego_graph_viz.ipynb) I used to generate them.

Another fun project that leverages this library is [FiML](https://github.com/whong92/FiML), this is a Django+React based browser app that I developed so I can have a fancy interface to my recommender models.