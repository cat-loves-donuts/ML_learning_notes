# What is KL divergence

**Translated from https://www.jianshu.com/p/43318a3dc715**

## What the hell is KL divergence

**Kullback-Leibler Divergence**, also called KL divergence is a method to quantifying the difference between two probability distributions P and Q, also known as relative entropy. In probability and statistics, we often use a simpler, approximate distribution in place of observed data or a distribution that is too complex. K-L divergence helps us measure the amount of information lost when using one distribution to approximate another.

We will mention why we use Cross-entropy rather than KL-divergence in deep learning.

### Here is a story

We are a team of space scientists, we discovered a new planet after a very very very..... long trip. On this planet, we found a vrery intersting creature, they are worms with teeth. We find that those worms have 10 teeth for each one of them, but many of them will loss some teeth because of the bad habbit of cleanning mouth and teeth. After collecting a lot of samples, we get the empirical distribution of teeth number in those worms mouth.

![75110-f1e63f2247d8a351](https://user-images.githubusercontent.com/43735308/155513169-650a287e-18ae-4053-a895-62e9e33ef794.jpg)

This is very valuable data, but still have some problems. Because we are very far away from the earth, it is really expensive for us to transport this data back. But, we are very smart, we approximate the original data with a simple model with only one or two parameters to reduce the data size. The simplest approximation model is the uniform distribution, since a worm does not have more than 10 teeth, so there are 11 possible values, and the probability that the worm has teeth is 1/11. The distribution diagram is as follows:

![75110-6f8844d5047fc1ee](https://user-images.githubusercontent.com/43735308/155513610-13b712bb-4b73-4b8c-9104-52073b3e8f79.jpg)



