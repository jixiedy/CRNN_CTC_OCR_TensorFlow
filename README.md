## CRNN_CTC_OCR_TensorFlow
This software implements the Convolutional Recurrent Neural Network (CRNN), a combination of CNN, RNN and CTC loss for image-based sequence recognition tasks, such as scene text recognition and OCR.

"An End-to-End Trainable Neural Network for Image-based Sequence Recognition and Its Application to Scene Text Recognition" : https://arxiv.org/abs/1507.05717

More details for CRNN and CTC loss (in chinese): https://zhuanlan.zhihu.com/p/43534801
## Dependencies
All dependencies should be installed are as follow:
* tensorflow==1.8.0
* opencv-python
* numpy


Required packages can be installed with
`pip install -r requirements.txt`
Note: This software cannot run in the tensorflow lastest version r1.11.0 since it's modified the tf.contrib.rnn API.

## Run Demo
Dowload pretrained model and extract it to your disc: [GoogleDrive](https://drive.google.com/file/d/1A3V7o3SKSiL3IHcTqc1jP4w58DuC8F9o/view)

Run inference demo:
```
python tools/inference_crnn_ctc.py \
  --image_dir ./test_data/images/ --image_list ./test_data/image_list.txt \
  --model_dir /path/to/your/bs_synth90k_model/
```

Result is:
`Predict 1_AFTERSHAVE_1509.jpg image as: aftershave`


![](https://github.com/ztoString/CRNN_CTC_OCR_TensorFlow/raw/master/test_data/images/1_AFTERSHAVE_1509.jpg?raw=true)


`Predict 2_LARIAT_43420.jpg image as: lariat`



![](https://github.com//ztoString/CRNN_CTC_OCR_TensorFlow/raw/master/test_data/images/2_LARIAT_43420.jpg?raw=true)
## Train a new model
### Data Preparation
* Firstly you need to download [Synth90k](http://www.robots.ox.ac.uk/~vgg/data/text/) dataset and extract it into a folder.
* Secondly supply a txt file to specify the relative path to the image data dir and it's corresponding text label.

For example: image_list.txt

```
90kDICT32px/1/2/373_coley_14845.jpg coley
90kDICT32px/17/5/176_Nevadans_51437.jpg nevadans
```

* Then you are suppose to convert your dataset into tensorflow records which can be done by

```
python tools/create_crnn_ctc_tfrecord.py \
  --image_dir path/to/90kDICT32px/ --anno_file path/to/image_list.txt --data_dir ./tfrecords/ \
  --validation_split_fraction 0
```
  
Note: make sure that images can be read from the path you specificed, such as:

```
path/to/90kDICT32px/1/2/373_coley_14845.jpg
path/to/90kDICT32px/17/5/176_Nevadans_51437.jpg
.......
```

All training image will be scaled into height 32 and write to tfrecord file.
The dataset will be divided into train and validation set and you can change the parameter to control the ratio of them.

Otherwise you can use the *create_synth90k_tfrecord.py* automatically create tfrecord

you can also use the *dowload_synth90k_and_create_tfrecord.sh* script automatically create tfrecord

## Train model
`python tools/train_crnn_ctc.py --data_dir ./tfrecords/ --model_dir ./model/ --batch_size 32`

After several times of iteration you can check the output in terminal as follow:

![](https://github.com/ztoString/CRNN_CTC_OCR_TensorFlow/raw/master/data/20190417.png)

During my experiment the loss drops as follow: 

![](https://github.com/ztoString/CRNN_CTC_OCR_TensorFlow/raw/master/data/CTC_LOSS.png)

And the learning rate drops as follow:

![](https://github.com/ztoString/CRNN_CTC_OCR_TensorFlow/raw/master/data/Learning_rate.png)

## Evaluate model

`python tools/eval_crnn_ctc.py --data_dir ./tfrecords/ --model_dir ./model/`

