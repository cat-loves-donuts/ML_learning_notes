# What is VQVAE and Autoregressive models?

**The related web pages: https://blog.csdn.net/StreamRock/article/details/93881187**
**https://blog.csdn.net/artistkeepmonkey/article/details/121023532**

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

![2222](https://user-images.githubusercontent.com/43735308/155452506-cb0f84d1-8d54-4be2-a61b-48b39b30004b.PNG)

### The work flow of VQ-VAE

1. Input x whose structure is [B, 3, 32, 32], B is the batch size.
2. After encoder, we get Ze(x) with [B, C=D, H, W] size, C means the Channels number of Conv layer in encoder and D means the dimensions of vectoe quantization, D is also the dimension of vectors which saved in Embedding. H and W mean the hight and width of the feature map after encoder.
3. Transpose Ze(x) to [B * H * W, D] and calculate the distense between the codes (B * H * W) and K vectors in Embedding based on Nearest neighbors algorithm.

![1](https://user-images.githubusercontent.com/43735308/155453699-1acb370d-2b7b-4e24-9cf6-28d70d66a522.PNG)

Func.1 means when we input x, the probability of z = k is: 

  1). when k is a vector index which has the colest distence to Ze(x) in vector sequence {e1, e2, ..., ek}, the conditional pobability is 1.
  
  2). otherwise is 0.
  
  The vector distence is measured by Euler distance in Func.1.
  
  ![3](https://user-images.githubusercontent.com/43735308/155494618-f206ef54-e83d-4fc2-8ca5-262a3b49d554.PNG)

  Func.2 means find the k by calcluating vector distence between vectore sequence and Ze(x) and use ek as the coding output zq(x).
  
4. zq(x) will be used as input of decoder and reconstruct image, output the p(x|z).

Another important part is loss function.

![4](https://user-images.githubusercontent.com/43735308/155495801-42a66fe5-2308-4959-adc6-83f1d7d8c886.PNG)

The first part repersents reconstruction error, this is a binary cross-entropy for Decoder. The second one is a loss used to update the dictonary elements in Embedding, sg[] means stop gradient which means do not operat the gradient back propagation, thus this part only works for dictionary elements (verctors) training. The last part is loss ofr Encoder, the official explaine of it is:

“Finally, since the volume of the embedding space is dimensionless, it can grow arbitrarily if the embeddings ei do not train as fast as the encoder parameters. To make sure the encoder commits to an embedding and its output does not grow, we add a commitment loss”

It means if ei cannot train as fast as encoder parameters, it could increase to any value. Therefore, to make sure that ecoder could give a reasonable embedding, we add a punishment on encoder. And the hyper parameter B (You know which parameter I mean right?) could be any value between [0.1, 2], they choose 0.25. 

Besides, when the NN backward, the reconstruction error gradient information will directly pass to Encoder, when there is a tranformation by Embedding. As the previous working flow of VQ-VAE shows, the red line is the gradient when backwards, Since the output representation of the encoder and the input to the decoder share the same D dimensional space, the gradients contain useful information for how the encoder has to change its output to lower the reconstruction loss.





