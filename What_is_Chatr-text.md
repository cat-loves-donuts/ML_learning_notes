# What is Chart-text

They used 2 CNN to classify the chart types and detect the content location. Then use OCR to get the information and connected compolents analysis to getthe bars. Then based on the type of chart to generate the text descreaption (Alternative Text).

## Steps

1. Used a MobileNet to classify the chart types.
2. Used a Faster-RCNN to detect the location of the content.
3. Used Tesseract OCR to get the text ingormation.
4. Used connection compolents analysis to extract different bars.
5. Based on the information and types of the chart generate text desceaption (based on different templates).

## Dataset

Because of using the CNN to do the classification and detection, need a lot of training data. Thus they Matplotlib to generate 5000 training and 1000 testing images for 5 different ytpe of chart: horizontal bar charts, vertical bar charts, stacked horizontal bar charts, stacked vertical bar charts and pie chart. And also use those data to train object detection NN with 7 classes: Chart title, X value (Except pie charts), Y value (Except pie charts), X label (Except pie charts), Y label (Except pie charts), Label (Only pie charts), Legend. 


## Classification

Used MobileNet to do classification. The NN pre-trained on ImageNet, the parameters are showing below:

Input size: ```224 X 224 X 3```

optimizer: ```SDG```

epochs: ```50```

LR: ```0.0001```

decay: ```30% each 20 epochs```

batch size: ```64```

## Detection

Used Faster-RCNN todo the detection task, because it has the higher accuracy on small objects.

Input size: ```keep aspect ratio but shorter side must bigger than 500 pixels```

epoch: ```50,000```

batch size: ```1```

anchor box size: ```0.25, 0.5, 0.75, 1.0, 1.5```

aspect tatios of BBox: ```0.5, 1.0, 2.0```

BBox threshold: ```0.5```

## Tesseract OCR

Using Tessereact OCR to get the text infprmation from the image.

1. Gray scale the image and threshold it to obtain a binary image. 
2. Fit a minimum bounding rectangle to the binary image and measure the angle made by the rectangle w.r.t. the horizontal. 
3. Rotate the image to horizontal.

Note: they found that hights is 130 pixel can siginificantly increase the OCR accuracy.

## Chart excraction

Based on differenttype of chart they applied different stratagies to exract chart information.

### Horizontal and vertical bar charts

Using OpenCV and DIPilb to do this.

1. Gray scale image and use median filter to remove noises.
2. Threshold the image and use area opening morphological operation to remove small objects.
3. label regions and use connection components analysis to get the bars.

### Stacked horizontal and vertical bar charts

Note: assume that there are no texts inside the stacked bars and each stack has a distinct color. After extracting the individual bars we use a local search algorithm to extract the stacks.





