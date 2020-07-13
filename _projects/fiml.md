---
title: "FiML"
excerpt: "An app to revolutionize film recommendation"
header:
  teaser: /assets/images/projects/fiml.png
classes: wide
# sidebar:
#   - title: "Summary"
#     image: /assets/images/EgoBigLebowski.png
#     image_alt: "logo"
#     text: "Designer, Front-End Developer"
#   - title: "Responsibilities"
#     text: "Reuters try PR stupid commenters should isn't a business model"
gallery:
  - url: /assets/images/projects/fiml.png
    image_path: /assets/images/projects/fiml.png
    alt: "fiml screenshot"
  - url: /assets/images/projects/fiml2.png
    image_path: /assets/images/projects/fiml2.png
    alt: "fiml screenshot 2"
---

Link to github project page: [here](https://github.com/whong92/FiML)

{% include gallery caption="Screenshot from the FiML webapp." %}

This is a web app that leverages the recommender models trained using [RecLibWH](https://github.com/whong92/recommender), to recommend movies. I wanted this app to set itself apart from the available recommendation platforms out there, by fulflling the following design objectives:

- Being highly customizable. The app should be able to respond to a user's instantaneous preferences, instead of returning a fixed list of mixed recommendations every time.
- Transparent. Allowing users to choose exactly what information goes into each recommendation. This point also plays into customizability as it allows users to tune inputs into the recommender.
- Highly responsive. Every rating / interation is taken into account instantaneously, in real time.

With the above objectives in mind, I created FiML - the **M**achine **L**earning **Fi**lm recommender app. The frontend and backends are powered by React and Django respectively, and leverages an adaptive variant of the 2007 Netflix Prize-winning recommender model.

The location of the live version is available upon request, since at this time, my resources for hosting the machine learning model is quite limited, and I would like to control the amount of users on the platform at any one time. I am actively working on trying to scale up the deployed version, however, so please stay tuned.