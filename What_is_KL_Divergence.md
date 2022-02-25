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

Well, of course, our raw data is not uniformly distributed. As a plan B (We always have plan B), another simple model that comes to our mind is the binomial distribution. There are a total of ```n=10``` alveoli in the worm's mouth, and the occurrence of teeth in each alveolar is an independent event, and the probability is ```p```. Then the number of worm teeth is the expected value ```E[x]=n*p```, and the actual expected value is the average value of the observed data, for example, 5.7, then ```p=0.57```, and the binomial distribution is:

![75110-8ad3782fe0220619](https://user-images.githubusercontent.com/43735308/155514335-0ba701aa-40ad-4de4-b114-9af9e853d5df.jpg)

Compared with the original data, both of them cannot repersent the original distribution very well.

![75110-b049f7fc560bd8d8](https://user-images.githubusercontent.com/43735308/155514431-cd996a45-620e-458c-81ed-fb21f6beaee7.jpg)

So, we need to figure out which distribution is much closer to the original one? Well, there are a lot of methods to measure the error, but what we want is to reduce the data size and transport model size. The previous 2 model restrict the number of parameters into 2 values, number of teeths and the probability (uniform distribution only need the number of the teeths). So which distribution can save more infromation from the original data? We could use KL-divergence.

### The entropy of the data

K-L divergence originates from information theory. Information theory mainly studies how to quantify information in data. The most important unit of information measurement is entropy, which is generally represented by ```H```. The formula for the entropy of a distribution is as follows:

![75110-7977551fba322af7](https://user-images.githubusercontent.com/43735308/155515459-b4e6e0e6-23f6-424d-b2cd-f084b1e1c788.jpg)

The log element do not have a certain base, the base could be ```2, e or 10```, etc. If we use ```2``` as base to calculate the H, we could take this ```H``` value as the minimum number of bits required to encode the information. Let's take the space worms as an example, information means the expirical distribution which we got from the observation gives the number of teeths. The entropy of the original probability distribution is 3.12 bits, it means the number of bits we need to encode th information of space worms teeth number probability.

But the entropy value do not tell us how to compress data to the minimum entropy or how to encode the data to get the optimal result (storage optimal). The main role of entropy is to tell us the theoretical lower bound (storage space) of the optimal encoding information scheme, and a way to measure the amount of information in the data. Understanding entropy, we know how much information is contained in the data, and now we can calculate how much information is lost when we use a probability distribution with parameters to approximate the original data distribution.

### Using KL-divergence to measure information loss

The ```p``` is the observed probability distribution, ```q``` is another approximate distribution used to approximate ```p```, then the KL-divergene of ```p``` and ```q``` is:

![75110-e94b5412d85e5698](https://user-images.githubusercontent.com/43735308/155649082-8edd8bd7-7c5e-4c7f-a0df-0a3712c02cd8.jpg)

Obviously, KL-divergence is the expectation of the log difference between the original distribution ```p``` and the approximate distribution ```q```. If we still use 2 as the base of log, then KL-divergence means the number of bits of information loss. We also use the follwoing way to repersent KL-divergence.

![75110-5e6109b0e9ec780a](https://user-images.githubusercontent.com/43735308/155666519-326c5061-8072-4aea-9175-c8e1986caee6.jpg)

![75110-d602c1e8d0fe3689](https://user-images.githubusercontent.com/43735308/155666571-21e40dd2-3fa7-4490-ad57-a0cc0bab1243.jpg)

### Compare 2 distribution

Let's calculate the KL-divergence between origianl distribution and Uniform distribution (**Using Uniform distribution to approximate original distribution**):

![75110-f682f83675fa42ee](https://user-images.githubusercontent.com/43735308/155667245-9bdc1643-1860-433b-8684-be149187c3e7.jpg)

And then the binomial distribution (**Using Binomial distribution to approximate original distribution**):

![75110-b7a85dc7ac779750](https://user-images.githubusercontent.com/43735308/155667286-de939f2b-1430-4a54-984b-4e8802e1a570.jpg)

We can see that using uniform ditribution has less information loss than using dinomial distribution. If we have to choose one between those 2 distribution, we should choose uniform distribution.

### Divergence is not distence

Normally, some "teammates" will take KL-divergence as a measurement of distance between different distribution. However, it is not, becuase according to the formula of KL-divergence it is easy to notice that KL-divergence does not symmetry (distance metric should be syemmetry). If we use **original distribution to approximate Binomial distribution**:

![75110-c72abbdf1938256e](https://user-images.githubusercontent.com/43735308/155668139-ee42d7f4-0401-40a7-ade5-6ae04e9b4c86.jpg)

Well, ```Dkl (Observed || Binomial) != Dkl (Binomial || Observed)```.

### Using KL-divergence to optimize models

The probability parameter we used for previous binomial distribution is ```p=0.57```, this is the mean value of the original data. ```p``` is between ```[0, 1]```, we need to choose a ```p``` value and construct a binomial distribution, our target is minimize the approximate error (KL-divergence). Thus what ```p``` we should choose?

This is the KL-divergence of original data distribution and binomial distribution when the parameter ```p``` is changing:

![75110-e6ca24b145a5ca8c](https://user-images.githubusercontent.com/43735308/155669257-4e67a8b2-e77f-4845-be76-0ae61118056a.jpg)

From the previous Pic, we could see that lowest KL-divergence located at the bottom of the curve, which ```p=0.57```. Thus our binomial distribution is the optimal distribution/ Please note that the binomial model is the best....

The previous situation we only considered binomial and uniform distribution model, then we will consider another model. First, let us seperate the original data into 2 parts, the first part only contains the probability of number of teeth from 0 to 5, the second part contains the probability of number of teeth from 6 to 10.

![75110-76a03ef0cd285801](https://user-images.githubusercontent.com/43735308/155669909-30bb32ee-a97a-4953-84d0-380a09248245.jpg)

It means the probability of a worm has number of teeth ```x=i``` is ```p/5```, the probability of ```x=j``` is ```(1-p)/6```, where ```i=0,1,2,3,4,5``` and ```j=6,7,8,9,10```. Aha, a new model borned and very strange.... This model only has one parameter  ```p``` and we could draw a KL-divergence curve like what we didi to binomial distribution:

![75110-dd374eaec9c5ad74](https://user-images.githubusercontent.com/43735308/155670391-c55c75df-22a1-4a52-a21a-af89a54ceb72.jpg)

Well, when ```p=0.47```, KL-divergence has the lowest value of **0.338**. Wait.... Farmiliar, right? Yes, this value is exactly same with the KL-convergence when we use uniform distribution (That doesn't mean anything). Let us continue generating the probability distribution of this strage model, it looks like uniform distribution indeed...

![75110-857b751b045a3aa2](https://user-images.githubusercontent.com/43735308/155670972-ca09572e-0eb3-4beb-9dd9-10f52f238b89.jpg)

Well, we all know that his model is strange, so when we have same KL-convergence, we will more likely to use more common and simple model like uniform distribution model.

Take a summary, we used KL-divergence as target function and find parameters in binomial distribution model ```p=0.57``` and our strage model ```p=0.47```. Yes, the main point in this section is **using KL-divergence as target function to optimaize models**.

### About entropy

**Information entropy**: When the encoding scheme is perfect, the shortest average encoding length.

**Cross entropy**: When the encoding scheme is not perfect (because the estimation of the probability distribution is not normally correct), the average encoding length. Is a loss function commonly used in neural networks.

**Relative entropy**: KL-divergence, the increase in the average encoding length relative to the minimum value when the encoding scheme is not perfect.

**Why we use corss-entropy rather than KL-convergence in loss function?**

KL-divergence = cross-entropy - entropy, ```DKL( p||q )=H(p,q)−H(p)```.

In the NN domains, ```H(p)``` will not change, thus ```DKL(p||q)``` equial to ```H(p,q)```.

## Last part

It is time to put some official things here

The defination of KL-divergence:

![75110-5d773218b2511d9a](https://user-images.githubusercontent.com/43735308/155671818-9fae3348-0a9e-4a46-99ab-21cfe9a26580.jpg)

Some notice when calculate KL-divergence；

![75110-f55d663d60503fa4](https://user-images.githubusercontent.com/43735308/155671870-d6c538c9-7609-405f-954f-7ddd9db79bf5.jpg)

How to deal with ```log0```:

![75110-a7dc83686d7206c1](https://user-images.githubusercontent.com/43735308/155671920-dd38e5dd-cda8-4dd7-b41c-d6d434626c68.jpg)








