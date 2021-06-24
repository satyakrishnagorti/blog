---
layout: post
title:  "Semi-Supervised Learning: A breif perspective on some recent methods"
date:   2021-06-24
categories: blog
img1: /blog/assets/blog/semi/1.png
img2: /blog/assets/blog/semi/2.png
---

Semi-supervised learning aims to learn from limited data by using both limited labelled data and unlabelled data efficiently to improve
the learning objective. In this blog post I will give some introduction on recent popular methods such as Mean-Teacher (MT) [1] and Virtual Adversarial Training (VAT) [2] and finally draw some conclusions on what some of these popular methods are actually trying to do. These methods have achieved 
some impressive results - CIFAR-10 dataset has 50000 train images, VAT uses only 4000 images to reach 90% accuracy. FixMatch [3] reduces this by a factor
of 100 by only using 40 images to achieve the same score, impressive!

Many semi-supervised methods can be seen as doing some form of label propagation. That is assign the same label to data points that are close together.
The way these methods achieve this may seem different, but in essence they can be seen as some kind of consistency regularization method.

To motivate this further, we know deep networks can produce some sharp decision boundaries. Having only a few training examples makes this even worse
as they are prone to overfitting. Hence we need a way of training the networks to force them to have smoother decision boundaries. The Mean Techer [1] paper has a nice example illustrating this.

![MT motivation]({{page.img1}}) 
Assume **Fig (a)** is the decision boundary of a regression problem, with the two blue dots as the outputs of labelled data points. In the absence of
regularization, we can see the decision boundary having a sharp increase in its outputs between these labelled points which might not be desirable.

Now adding some regularization like dropout or input perturbation will make sure the region around a labelled data point behaves consistently as shown
in **Fib (b)**. Here the smaller blue dots represent the outputs of the perturbed data points.

Now these two figures explain concepts that we are already familiar with and use to train our deep learning models. The question now is how do we further use unlabelled data points to achieve something similar? Mean-Teacher tries to achieve this by using two different networks: a teacher network and a student network as shown in the figure below.

![MT]({{page.img2}}) 

Both the teacher and the student networks share the same parameters. The student network outputs are perturbed using noise (similar to Ladder Networks[4]). The student network can be trained using the cross-entropy objective in the case of a classification setting which achieves similar outcomes as illustrated in **Fig (b)**. And for the unlabelled data, we can force the student to produce the same output as the teacher using a concistency regularization term $$C_t$$. This way neighborhood around
an unlabelled data point shown as a black circle in **Fig (c)** remains consistent with the teacher's output.

The consistency regularization term can take many forms such as the KL divergence