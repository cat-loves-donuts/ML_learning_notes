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







