# Deep Image Matting
This is tensorflow implementation for paper "Deep Image Matting".

Thanks to Davi Frossard, "vgg16_weights.npz" can be found in his blog:
"https://www.cs.toronto.edu/~frossard/post/vgg16/"

**2017-8-25:**
Now this code can be used to train, but the data is owned by our company. I'll try my best to provide code and model that can do inference. Fix bugs about memory leak when training and change one of randomly crop size from 640 to 620 for boundary security issue.This can be avoid by preparing training data more carefully. Besides, it can save model and restore pre-trained model now, and can test on alphamatting set at rum time.

**2017-9-1:**
Validation code and tensorboard view on 'alphamatting' dataset are added. Some bugs on compositional_loss and validation code are  fixed. Missed 'fc6' layer is added now. And the decoder structure is exactly same with paper despide of replacing unpooling with deconvolution layer which means the network is more complex than before. The weight Wi of two loss is still vague, we are trying to find best weight structure. Currently, general boundary is easy to predit. But some details or complex foregrounds like bike is still bad. 

**2017-9-14:**
Latest version of code has following changes:    
1. Rearrange the order of preprocessing so that there is no ground truth shift in preprocessing.(Composite bg,fg,alpha first then resize, or resize bg,fg,alpha then composite. My suggestion is that composition should always happen after resize.) The result RGB images of those two preprocessing order are slightly different from each other, although it's hard to tell the difference by eye.)       
2. Replace deconvolution with unpooling. Because in the experiment, it is shown that deconvolution is always hard to learn detailed information (like hair). And because of using unpooling, batch_size is also changed from 5 to 1 ( The code is not decent now, just can work).    
3. Another thing need to mentioned here is that when we training on single complex sample like bike, even with deconvolution (not unpooling), the network can overfitting. But deconvolution can't converge on whole dataset. (Maybe I didn't training enough time : lr = 1e-5 with 5 days training, can't converge). Discussion about 'whether deconvolution can replace unpooling' is welcomed!
4. Add hard mode to allow training on tough samples

<h2>Usage</h2>
simply run:<br />
python test.py --alpha --rgb<br /> 
sample:<br />
python test.py --alpha=./test_data/alpha/1.png --rgb=./test_data/RGB/1.png<br />

<h2>Pretrained Model</h2>
Can be found here:<br />
https://drive.google.com/open?id=0B6l9O8aWij8fSnZBa2o4OUpsb1k <br />
notice that you have to create a folder './model' under root and then put those pretrained model in there.<br />

<h2>Important notification:</h2>
1. The pretrained model is trained on private dataset, which has large difference from authors data, so it performs a little struggling on author's data. You can test the model by feeding test_data.<br />
2. 'fc6' is transformed into convolution operation by tricks proposed in FCN paper. This paper also follows this way. But in this code, convolutionarized 'fc6' is replaced by plain convolution whose weights and biases are initialilzed randomly.<br />
3. Even test on our own data, this model still can't reach the performance mentioned in paper.<br />
