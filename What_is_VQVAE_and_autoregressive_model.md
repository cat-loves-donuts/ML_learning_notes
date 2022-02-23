# What is VQVAE and Autoregressive models?

**Vector Quantised-Variational AutoEncoder (VQ-VAE)** was first proposed in paper "Neural Discrete Representation Learning". One big different of VQ-VAE and VAE is that:

VAE assumes the hidden vectors distribution follows Gaussian distribution.

VQ-VAE assumes the hidden vectors distribution follows Class distribution.

Before we talk about VQ-VAE, we need to talk about Autoregression models like PixelRNN and PixelCNN.

## Autoregression models(AR models)

The basic idea of VQ-VAE is based on AR models, we could say that the idea of using VQ-VAE on generation task is based on PixcelRNN and PixcelCNN. This kind of AR models focusing on generating images and the images are discrete. Take CIFAR-10 as example, the image in CIFAR-10 is 32X32 with 3 channels and we could make it equail to a 32X32X3 matrix, eah element in this matrix is a int number between 0 to 255. Thus this matrix could also equail to a 32X32X3 = 3072 length sentence, and the size of words is 256. Based on this idea, we could use NLP model idea to generate the image pixcel by pixcel. We input the previous all pixcel values and generate the next one pixcel (This is how AR models working).

![autoregressive](https://user-images.githubusercontent.com/43735308/155282713-ccdd303d-c356-4d53-b1a7-0b886edf9269.png)

p(x)=p(x1)p(x2|x1)…p(x3n2|x1,x2,…,x3n2−1), each one is a 256 classification problem, but the dependency is different.

![fvsbn](https://user-images.githubusercontent.com/43735308/155282861-e1f55aa2-5c1a-4b7c-ac0d-6f0eb356e220.png)

This is a fully visible sigmoid belief network over four variables. The conditionals are denoted by x1ˆ,x2ˆ,x3ˆ,x4ˆ respectively.

The AR method is safe and could do probability estimation properly. But it has a very big drawback, the speed is too slow. Because how it works is generate pixcel by pixcel, thus it need to do random sampling on each pixcel. Te previous CIFAR-10 example is too small, currently we are normally using at least 256X256X3 size which have nearly 200,000 pixcels, just imagin a sentence with length of 200,000. Besides, as a sequnce this long, no matter you ar using RNN or CNN, both of them cannot capture the dependencies very well under this length. Tansformer also needs to facing a large calculation times (O(n^2)).

There is another problem of AR model, it broken the links between categories. Although each pixcel in image is discrete and we could make it as a 256 classes classification problem. But, in fact, the nearby pixcels have very little difference.On the mathmatical way, our objective function, cross entropy, is -logP(t), assume that the target pixcel value is 100 and prediction is 99, the P(t) will be 0 because the category is different and -logP(t) will be very large and bring a large loss. But according to the visilization, the pixcel value of 100 and 99 do not have big differences, human eyes sometmes cannot identify the differences. And that means we should not get this kind of big loss.

## How VQ-VAE works

Based on the problems, VQ-VAE proposed a solution: dimensional reduction first, then model the encoded vector with PixelCNN.

### Dimensionality reduction discretization





