# Attention OCR — study repository

This repository contains an existing attention-based OCR implementation and an accompanying `Attention_OCR_Masterclass.ipynb` notebook. It is not a claim of original authorship of the underlying OCR library.

## Attribution and local work

- The package metadata identifies [Ed Medvedev's attention-ocr](https://github.com/emedvedev/attention-ocr) as the upstream project.
- The preserved [MIT license](LICENSE.md) credits Qi Guo and Yuntian Deng.
- The local history includes the addition of `Attention_OCR_Masterclass.ipynb`; consult the notebook and commit history for this repository's study material.
- The inherited implementation targets TensorFlow 1.x. Compatibility with current TensorFlow versions is not established here.

The original usage documentation follows below. Its package installation command refers to the published `aocr` package, not necessarily this checkout.

---

## Installation

```
pip install aocr
```

Note: Tensorflow and Numpy will be installed as dependencies. Additional dependencies are `PIL`/`Pillow`, `distance`, and `six`.

Note #2: this project works with Tensorflow 1.x. Upgrade to Tensorflow 2 is planned, but if you want to help, please feel free to create a PR.

## Usage

### Create a dataset

To build a TFRecords dataset, you need a collection of images and an annotation file with their respective labels.

```
aocr dataset ./datasets/annotations-training.txt ./datasets/training.tfrecords
aocr dataset ./datasets/annotations-testing.txt ./datasets/testing.tfrecords
```

Annotations are simple text files containing the image paths (either absolute or relative to your working dir) and their corresponding labels:

```
datasets/images/hello.jpg hello
datasets/images/world.jpg world
```

### Train

```
aocr train ./datasets/training.tfrecords
```

A new model will be created, and the training will start. Note that it takes quite a long time to reach convergence, since we are training the CNN and attention model simultaneously.

The `--steps-per-checkpoint` parameter determines how often the model checkpoints will be saved (the default output dir is `checkpoints/`).

**Important:** there is a lot of available training options. See the CLI help or the `parameters` section of this README.

### Test and visualize

```
aocr test ./datasets/testing.tfrecords
```

Additionally, you can visualize the attention results during testing (saved to `out/` by default):

```
aocr test --visualize ./datasets/testing.tfrecords
```

### Export

After the model is trained and a checkpoint is available, it can be exported as either a frozen graph or a SavedModel.

```bash

# SavedModel (default):
aocr export ./exported-model

# Frozen graph:
aocr export --format=frozengraph ./exported-model

```

Load weights from the latest checkpoints and export the model into the `./exported-model` directory.

**Note**: During training, it is possible to pass parameters describing the dimensions of the input images (`--max-width`, `--max-height`, etc.). If you used them during training, make sure to also pass them to the `export` command. Otherwise the exported model will not work properly when serving (next section).
