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

They are using VQ-VAE to repersent the images. Basicly is using a 





