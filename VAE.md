# VAE

***If your are interested, please check the original link. I translated the original web page by meself, so please ignore the typo or whatever...***

**original link (Chinese):http://www.gwylab.com/note-vae.html**

**Variational Auto Encoder (VAE)** is a generation model, the basic idea of VAE is very simple, but we need to introduce the idea of **Auto Encoder (AE)** first: transforme a bunch of real samples into a ideal data distribution through the encoder, and then pass this data distribution to a decoder
to get a bunch of generated samples. If the generated samples are close enough to the real samples, then we trained a AE. VAE adds a variational operation based on AE, make the output
of the encoder could correspond to the mean and variance of the target distribution.

![image001](https://user-images.githubusercontent.com/43735308/155079700-8d7347ab-13d8-4500-a62b-f78821f0c41e.jpg)

## The design of VAE

The problem that VAE want to solve is how to construct a encoder and decoder so that they could easily encode the image into a repersentive form and this form can be decoded back to the original
real image as losslessly as possible.

This sounds like **PCA**, but PCA is used to do matrix dimensionality reduction:

![image002](https://user-images.githubusercontent.com/43735308/155080645-cdc86f4d-3e95-4cd3-901e-81893198163c.jpg)

Like the image shows, X is a matrix and become a low dimensional matrix through W. Because this is a linear process, we could transform back a X^ through a WT. Now, we need to find a transformation
W that could make matrix X and X^ as same as possible, and this is what PCA is doing. How we find this W in PCA is using **SVD (singular value decomposition)** and this is a pure mathmatical
method. We will use Neural Network to replace it in VAE.

If we change the marix X in PCA to a image, change W to a encoder, lower dimensional matrix c as a encode and WT and X^ are decoder and generated image. Then a PCA become a "naive" AE. 

![image005](https://user-images.githubusercontent.com/43735308/155081916-236696d2-1efb-4e1e-9def-81ab6c1d769c.jpg)

Now we will do some modification on this "naive" AE, we replace W and WT with neural networks and we get **Deep AE**:

![image006](https://user-images.githubusercontent.com/43735308/155082195-eca35180-921a-4701-a470-4a78f6d378bb.jpg)

The advantage of this model is that we introduced powerful fitting ability of NN into AE so that the dimension of the code(c) could much smaller than the original image(X). In MNIST
dataset, Deep AE could compressed a 784 dimensional vector(28*28) to 30.

Currenly we got a AE model which has good ability to reconstruct clear images. But this is not the end, because, for a generation model, the decoder part should have the ability to work
indipendently and could generate a clear and real image no matter what code you put in under specific dimension restriction. Let us analyze why whis happend.

Let's make an assumption that we have 2 training pictures (normally we have much more training data....), one is full moon and another one is half moon. According to our training, the AE could reproduce both image very perfectly.

![image008](https://user-images.githubusercontent.com/43735308/155088931-bb881d53-7413-4663-a062-4d0a781e9eab.jpg)

Then, we choose a point on the coding space and give it to our AE, we hope it could give us a nice and clear picture which contains a 3/4 moon. But, in fact, what we will get is
a fuzzy and un-recongnized picture. A reaonable explaine is that becase the encoding and decoding process both through the NN, this is process is a non-linear process. Therefore, the migaration between points in the code space is very irregular.

One way to solve this is including noise, make the coding space bigger and could cover the empty encoding points on coding space.

![image009](https://user-images.githubusercontent.com/43735308/155089603-368492e5-6d0c-4282-a49f-65c3952abc0c.jpg)

As the image shows, we added some noise when we coding, make sure that the coding points for each picture will located in the green part. Thus, during the training process, all the points in the green part could be sampled, therefore, the decoder will try to reconstructed all the points in green range to the original picture. Then we could focusing on the previous distortion point, it located between full moon and hal moon, thus out decoder will try it best to reconstruted the picture back to full moon and also the half moon. Thus it will choose a middel solution which is a 3/4 moon.

It is clear that we could use some noise in encoder to cover the distortion regions. But this is still not enough, because on the yellow point which is located very far away from the green part, AE still cannot sample the yellow point. To solve this, we could try to pull the nise range as long as possible to cover the whole coding space, but we have to make sure that the coding near the original coding ahve higher possibility. The further the point away from the coding points, the lower possibility it has. And this is the method to make the image coding become continues from discrete and this is also the main idea of VAE.

![image010](https://user-images.githubusercontent.com/43735308/155097539-5b078fbf-9698-40f2-9e8d-38a63b825e5c.jpg)

## The structure of VAE
![image011](https://user-images.githubusercontent.com/43735308/155097814-f4c4b883-b7b1-45a5-873b-c3e4dc41ba37.jpg)

This is the structure of VAE. Let's make it simple:

In AE, encoder will generate a code directly, but in VAE, to add suitable noise for codes, the encoder will output 2 codes, one is the original code(m1, m2, m3) and another one is code used to control the degree of noise(a1, a2, a3) **(Sorry guys, I cannot find the greek symbol)**, then we also have a stochastic noise code(e1, e2, e3). The code(a1, a2, a3) is used to give weights for stochastic noise code(e1, e2, e3). The reason why add exp(ai) is to guarantee the assigned weight is a positive value. And finally, add the original code(m1, m2, m3) with noise code to get the VAE code(c1, c2, c3). Other things are similar with Deep AE.

For loss funtion, VAE adds a loss function Minimize2, this is essential. To make the reconstructed images have higher quiality, AE will make the noise have the lowest influence on reconstructed images, thus it will give lower weights on noise, thus is only need to make (a1 ,a2, a3) be the negative infinity values. So the second loss(Minimize2) is used to limit the AE to do such extreme operation. It is clear that exp(ai) - (1 + ai) will get lowest when ai=0, thus (a1,a2,a3) will no be assigned to negative infinity values.

The following parts will be the detailed mathmatical reasoning of VAE.

## How VAE works

As we all know, for generation models, our mainestream models include **Hidden Markov model(HMM)**, **Naive Bayes Model(NB)** and **Gaussian Mixture Model(GMM)**. And VAE belongs to GMM.

What is GMM? **The distribution of any data can be regarded as the superposition of several Gaussian distributions**.

![image017](https://user-images.githubusercontent.com/43735308/155103099-953779ae-19c2-4c7c-a860-fbcc4a5d3b7c.jpg)

If P(x) in the previous pic repersents a distribution, then there is a method could take it apart into multiple Gaussian disstribution like the blue lines. Well, there is a prof that when the number of splites reaches 512, the superposition of several Gaussian distributions will have very very small error with the original distribution.

Thus we could use this theory to code data. The must directly way is that use a set of Gaussian distributions parameters as encode value.

![image018](https://user-images.githubusercontent.com/43735308/155103982-86cc42b5-63bc-45d0-be04-9ab1c10f2594.jpg)

Like what pic shows, m means a index in coding dimension. If we have 512 dimensional code, m should between 1 and 512. The m will follow a probability distribution P(m) (multinomial distribution). Now, the corrisponding relationship is, for every sampled m, it will corispond to a small Gaussian distribution N(um,Em), P(X) could equial to the sum of those Gaussian distributions.

![image020](https://user-images.githubusercontent.com/43735308/155104806-9126fc3e-495c-4f65-b02e-55f7da568c42.png)

And ![image021](https://user-images.githubusercontent.com/43735308/155105021-56421065-1d81-40c7-94dd-b4c2e4b849f7.png), ![image022](https://user-images.githubusercontent.com/43735308/155105064-106e36a9-0741-437c-9a47-7e91cdb5d8d3.png)

The previous method is very simple and directly, it based on the coding method which is used for discrete and have heavily distorted regions we mentioned before.

![image023](https://user-images.githubusercontent.com/43735308/155105432-47d45435-1c49-4bf7-8cd8-13bc6ca06331.jpg)

Now, we change our code into a continuous variable z, we make a rule that z will follow a normal distribution N(0,1) (In fact, other continous distribution is also ok). For each sample z, there are 2 parameters u and a to repersent the mean and variation, respectively, when z correspond to Gaussian distribution. Then the sum of all Gaussian distributions in the integration domain becomes the original distribution P(X).

![image027](https://user-images.githubusercontent.com/43735308/155106556-6745ab53-1556-449c-8bd0-78abac1af1f1.png)

And ![image028](https://user-images.githubusercontent.com/43735308/155106616-03aa9b71-5d80-4116-82a9-8b79cb4f8e69.png), ![image029](https://user-images.githubusercontent.com/43735308/155106648-c013ef35-1053-46e7-8076-998f557fd296.png)

Because P(z) is already knowen value, P(x|z) is unknowen but ![image029 (1)](https://user-images.githubusercontent.com/43735308/155106873-7545f4fd-7883-49f4-aa43-47d39125cfaf.png), thus what we really need to know is the repersentation functions of u and a. Bcause P(x)is very comlecated normally and u and a are very hard to calculate, thus we use NN to help us.

The first NN called Decoder, it will find out u and a, this equial to get p(x|z).

![image033](https://user-images.githubusercontent.com/43735308/155107296-ecc5617d-7aae-4cbd-8360-fc3af3c81b58.jpg)

The second one is called Encoder, it will find **q(z|x)** (We will mention is one later in the equiation), q can repersent any distribution.

![image036](https://user-images.githubusercontent.com/43735308/155107586-f64a5f3e-abea-44f3-8af7-a28739538293.jpg)

The reason why we need the second NN Encoder is taht we need it to help Decoder to find P(x|z).

Thus we need to get the result of 

![image027 (1)](https://user-images.githubusercontent.com/43735308/155107827-13c94814-51f0-407c-9e99-a30bfc287113.png)

We hope P(X) as bigger as possible, this equial to: 

![image038](https://user-images.githubusercontent.com/43735308/155107949-aa0097a6-6b9d-4c41-a890-dc74f7ae85c4.png)

Notice:

![image039](https://user-images.githubusercontent.com/43735308/155108040-f47409a9-0120-4435-a571-10edcd4c4f22.png) (q(z|x) coud be any distribution)

![image040](https://user-images.githubusercontent.com/43735308/155108237-1b396915-17ff-4541-9e25-422abb085504.png)

![image041](https://user-images.githubusercontent.com/43735308/155108257-4668ac11-9df3-4bc7-9efb-dfdf84156533.png)

![image042](https://user-images.githubusercontent.com/43735308/155108269-fe80a667-8711-4f2b-a53e-49daa5e211d3.png)

![image043](https://user-images.githubusercontent.com/43735308/155108272-676f12a2-29d3-4928-906e-0338df92ffbd.png)

The second value in the previous equiation is bigger or equail to 0, thus we could get the nether of logP(x):

![image045](https://user-images.githubusercontent.com/43735308/155108509-5e08e574-f5d1-4679-8d23-c850bc5a0efc.png)

We make the nether as ![image046](https://user-images.githubusercontent.com/43735308/155108755-add9df4a-2b7b-4ec0-822d-87600a509392.png)

Then, the original equation will be ![image047](https://user-images.githubusercontent.com/43735308/155108802-6b116d98-aa54-4f01-beef-23d357ce9066.png)

What we want  is getthe P(x|z) to make the logP(x) as big as possible, now we have a q(z|x) and the q(z|x) will maximize the logP(x). Let us see the relationship of logP(x) and Lb:

![image051](https://user-images.githubusercontent.com/43735308/155109152-44a3ea0d-6749-4043-a133-09905907dcf5.jpg)

One interesting thing is that when we keep the P(x|z) not change, because logP(x) only related to P(x|z), thus the value of logP(x) will not change. Then if we change the value of q(z|x) to make Lb bigger and the KL divergence smaller, when we make the q(z|x) equail to P(z|x), KL divergence will become 0, LB will totalluy equial to logP(X). Thus we can say that no matter how logP(x) changes, we can always modify out parameters to make Lb equial to logP(x), because Lb is the nether of logP(x), to get the result of Maximum logP(x) is equial to get the result of Maximum Lb.

From a macro perspective, modify P(x|z) is change Decoder, modify q(z|x) is modify Encoder. Therefore, the training logic of VAE becomes, every one step forward of Decoder will make Encoder to modify itself to the same of Decoder. It is like Encoder holding a rifle behind Decoder, when the next the Decoder is training, Decoder annot step back....

Now let us get Maximym Lb:

![image057](https://user-images.githubusercontent.com/43735308/155110956-f333d9d4-539e-4c05-a0bb-c10e228994c8.png)

![image058](https://user-images.githubusercontent.com/43735308/155110965-69b89952-2b89-4f0f-8d9e-17414894096d.png)

![image059](https://user-images.githubusercontent.com/43735308/155110971-ae854561-13de-42a7-983e-f5a7084ce548.png)

![image060](https://user-images.githubusercontent.com/43735308/155110974-a8c2b2f7-b8b8-411e-93e9-574d1613a67e.png)

Thus, what we need to solve is the minimum value of ![image061](https://user-images.githubusercontent.com/43735308/155111090-0367033b-25d6-4fa3-b810-436eaf9a3223.png) and the maximum value of ![image062](https://user-images.githubusercontent.com/43735308/155111153-cbe7f7dd-a81d-474b-a104-565b3bc6ddeb.png).

The fisrt equiation equail to:

![image063](https://user-images.githubusercontent.com/43735308/155111232-b702943a-c8df-4335-b5e8-510d4f4d3eb8.png)

Thus this equiation is the loss function (Minimized2) of VAE in second section. 

The second equiation:

![image064](https://user-images.githubusercontent.com/43735308/155111641-8cc33712-b20e-417f-8f74-96c5c01021a1.png)

![image065](https://user-images.githubusercontent.com/43735308/155111654-afe7f3ef-b5d0-4ffa-9460-de9c0fc71590.png)

The above expectation means that the value (the output of decoder) shoud be as high as poosible when given a condition (the output of encoder). It is similar to a AE loss if we do not count in the variance:

![image067](https://user-images.githubusercontent.com/43735308/155111999-51c42e3e-2524-4021-bb2a-8ac8c632d52b.jpg)

Thus, this equiation is the first loss function (Minimized1) in VAE struture in section 2.

