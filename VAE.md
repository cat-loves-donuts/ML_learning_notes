# VAE

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

It is clear that we could use some noise in encoder to cover the distortion regions. But this is still not enough, because on the yellow point which is located very far away from the green part, AE still cannot sample the yellow point. To solve this, we could try to pull the nise range as long as possible to cover it. For each





