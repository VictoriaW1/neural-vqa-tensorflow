# Visual Question Answering in Tensorflow

This is a Tensorflow implementation of the VIS + LSTM visual question answering model from the paper [Exploring Models and Data for Image Question Answering][1]
by Mengye Ren, Ryan Kiros & Richard Zemel. The model architectures vaires slightly from the original - the image embedding is plugged into the last lstm step (after the question) instead of the first. The LSTM model uses the same hyperparameters as those in the [Torch implementation of neural-VQA][2]. 
![Model architecture](http://i.imgur.com/Jvixx2W.jpg)

## Requirements
- Python 2.7.6
- [Tensorflow][3]
- [h5py][4]


#### Datasets
- Download the [MSCOCO][5] train+val images and [VQA][6] data using `Data/download_data.sh`. Extract all the downloaded zip files inside the `Data` folder.
- Download the [pretrained VGG-16 tensorflow model][7] and save it in the `Data` folder.

## Usage

- Extract the fc-7 image features using:
```
python extract_fc7.py --split=train
python extract_fc7.py --split=val
```

- <b>Training</b>
  * Basic usage `python train.py`
  * Options
    - `rnn_size`: Size of LSTM internal state. Default is 512.
    - `num_lstm_layers`: Number of layers in LSTM
    - `embedding_size`: Size of word embeddings. Default is 512.
    - `learning_rate`: Learning rate. Default is 0.001.
    - `batch_size`: Batch size. Default is 200.
    - `epochs`: Number of full passes through the training data. Default is 50.
    - `img_dropout`:  Dropout for image embedding nn. Probability of dropping input. Default is 0.5.
    - `word_emb_dropout`: Dropout for word embeddings. Default is 0.5.
    - `data_dir`: Directory containing the data h5 files. Default is `Data/`.

- <b>Prediction</b>
  * ```python predict.py --image_path="sample_image.jpg" --question="What is the color of the animal shown?" --model_path = "Data/Models/model25.ckpt"```
  * Models are saved during training after each of the complete training data in ```Data/Models```. Supply the path of the trained model in ```model_path``` option.
  
- <b>Evaluation</b>
  * run ``python evaluate.py``` with the same options as that in train.py, if not the defaults.

## Implementation Details
- fc7 relu layer features from the pretrained VGG-16 model are used for image embeddings. I did not scale these features, and am not sure if that can make a difference.
- Questions are zero padded for fixed length questions, so that batch training may be used. Questions are represented as word indices of a question word vocabulary built during pre processing.
- Answers are mapped to 1000 word vocabulary, covering 87% answers across training and validation datasets.
- The LSTM+VIS model is defined in vis_lstm.py. The input tensors for training are fc7 features, Questions(Word indices upto 22 words), Answers(one hot encoding vector of size 1000). The model depicted in the figure is implemented with 2 LSTM layers by default(num_layers in configurable).

## References
- [Exploring Models and Data for Image Question Answering][1]
- [Torch implementation of VQA][2]
- [Neural Caption Generator with Attention][8]

[1]: http://arxiv.org/abs/1505.02074
[2]: https://github.com/abhshkdz/neural-vqa/
[3]: https://github.com/tensorflow/tensorflow
[4]: http://www.h5py.org/
[5]: http://mscoco.org/
[6]: http://visualqa.org/
[7]: https://github.com/ry/tensorflow-vgg16
[8]: https://github.com/jazzsaxmafia/show_attend_and_tell.tensorflow
