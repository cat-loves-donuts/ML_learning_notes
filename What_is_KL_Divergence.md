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

Well, of course, our raw data is not uniformly distributed. As a plan B (We always have plan B), another simple model that comes to our mind is the binomial distribution. There are a total of n=10 alveoli in the worm's mouth, and the occurrence of teeth in each alveolar is an independent event, and the probability is p. Then the number of worm teeth is the expected value E[x]=n*p, and the actual expected value is the average value of the observed data, for example, 5.7, then p=0.57, and the binomial distribution is:

![75110-8ad3782fe0220619](https://user-images.githubusercontent.com/43735308/155514335-0ba701aa-40ad-4de4-b114-9af9e853d5df.jpg)

Compared with the original data, both of them cannot repersent the original distribution very well.

![75110-b049f7fc560bd8d8](https://user-images.githubusercontent.com/43735308/155514431-cd996a45-620e-458c-81ed-fb21f6beaee7.jpg)

So, we need to figure out which distribution is much closer to the original one? Well, there are a lot of methods to measure the error, but what we want is to reduce the data size and transport model size. The previous 2 model restrict the number of parameters into 2 values, number of teeths and the probability (uniform distribution only need the number of the teeths). So which distribution can save more infromation from the original data? We could use KL-divergence.

### The entropy of the data

K-L divergence originates from information theory. Information theory mainly studies how to quantify information in data. The most important unit of information measurement is entropy, which is generally represented by H. The formula for the entropy of a distribution is as follows:

![75110-7977551fba322af7](https://user-images.githubusercontent.com/43735308/155515459-b4e6e0e6-23f6-424d-b2cd-f084b1e1c788.jpg)











