# What is ERNIE-VILG

ERNIE-VILG is a bidirectional vision-language generation model which proposed by Baidu.
paper: https://arxiv.org/pdf/2112.15283.pdf
Based on blog: https://blog.csdn.net/weixin_42001089/article/details/122364451

## Data-set source:

1. Chinese Webpages (include imgaes with context and discreaption)
2. Image Search Engine (based on quick-clicked logic to collect data)
3. Public image-text Dataset (CC, CC12M)

## Architecture

![1](https://user-images.githubusercontent.com/43735308/156098582-7ebad1f8-6cee-44f8-8871-ce7a515a22ea.PNG)

ERNIE-VILG used transformer same in UniLM and BERT, which no encoder or decoder but using mask to achive the trainig and testing aims. For more details please check UniLM.

In the previous Pic we could see that the left blue block means using transformer to do image-to-text task and the right one is doing text-to-image task. And the two transformer share parameters.

### Image Discrete Repersentation

They are using VQ-VAE to repersent the images. VQ-VAE is similar to VAE which has encoder and decoder. For VQ-VAE, the encoder and decoder are both multiple Conv layers, after get the feature map from the encoder, VQ-VAE will use nearest neighbor to find the closest vector in the codebook and replace the original features in the feature map with codebook vectores (codebook vectores has same dimenssions with feature map). Then use the decoder to DeConv them back to images. You could check previous page for more details: https://github.com/cat-loves-donuts/ML_learning_notes/blob/main/What_is_VQVAE_and_autoregressive_model.md

If the input is a image, they use a VQ-VAE to get the featuer map and replace the feature map with the featuer map vectors with index of the code book to generate a discrete repersentation of the image and flatten, then input to transformer. 

### Bidirectional Generative model

The transformer architecture is similar with BERT and UniLM, which have 2 inputs, source tokens and target tokens. The source tokens are allowed to attend all the source tokens and the target tokens are allowed to attend the source tokens and the target tokens lie left to them. (You can check UniLM:https://github.com/cat-loves-donuts/ML_learning_notes/blob/main/What_is_UniLM.md for more details)

Using text tokens combine with image tokens for text-to-image task and using image tokens combine with text tokens for image-to-text tasks. The loss is showing below:

![2](https://user-images.githubusercontent.com/43735308/156121906-2f6a0273-b18a-430f-87e6-caec801967a5.PNG)

It is a generation loss but, because of parameter sharing, they sum them up.

### Mask design

Becuse the length of the image tokens are longer than text tokens, it will use a long time and large memory to train and inference. Therefore, they used a sparese attention method to speed up by 25% and save 50% of GPU memory. 

![3](https://user-images.githubusercontent.com/43735308/156122621-d826734c-df11-436d-b850-a82ea311c6a4.PNG)

For ```i```th transformer layer, using row attention (i mod 4 !=2), using column attention (i mod 4 =2) and comvolutional attention (the last layer). The previous Pic shows a hypothetical version which has 6 text tokens and 16 image tokens (4X4) and convolutional kernel size of 3X3. The actual kernel size of our model is 11×11 following DALL-E.

### Text-to-image Synthesis

The most important thing is that they proposed a method to unified training the transformer and the deocder of VQ-VAE.

Mostly for this part, we will seperate the model into 2 stages, discrete reperesentation sequence generation and image reconstruction, and train them separetly, like the red line in Pic.1. We firstly get the repersentation and map the index back to the codebook to find the feature vectors and then reconstruct the image and these 2 stage cannot train together because we have a mapping operation. 

What they do is after the last layer of transformer, they use a MLP layer to mapping the index back to the feature map, thus the gradient can do back propagation and train the model end-to-end.

**Interesting thing is that the encoder part (quantization part) are pre-trained and fixed during the image-to-text traing task.**

### Large-scale training

They implement their 10-bilion parameter ERNIE-ViLG model on PaddlePaddle platform. The original discreption is:

"we adopt Group Sharded data parallelism techniques [42, 43] to eliminate memory redundancies by partitioning the optimizer states, gradients, and parameters across multiple devices. In addition, activation recomputation [44] and mixed-precision [45] are applied to reduce the GPU memory footprint and increase throughput. Moreover, Optimizer-offload [46] is introduced to swap the sharded optimizer states and master parameters to the CPU which largely reduces the GPU memory footprint. Furthermore, we combine Optimizer-offload with Gradient Accumulation to bring down the communication frequency between CPU and GPU, delivering a higher computation efficiency."

### Experiment

They used VQGAN, an enhanced variant of VQ-VAE as image tokenizer, some parameters are showing:

#### VQGAN

**f** (denotes the reduction factor in the side-length): 8
**vocab size**: 8192

They pre-process the original image to 256 * 256 through center crop, and thus the length of the visual discrete token sequence n is 1024 (h × w, h = w = 32).

#### Transformer

**transformer layers**: 48
**hidden units**: 4096
**attention heads**: 64







