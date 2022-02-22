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

Let's make an assumption that we have 2 training pictures (normally we have much more training data....), one for 



