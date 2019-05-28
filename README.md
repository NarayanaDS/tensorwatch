# Welcome to TensorWatch

TensorWatch is a debugging and visualization tool designed for deep learning and reinforcement learning from Microsoft Research. It works in Jupyter Notebook to show real-time visualizations of your machine learning training and perform several other key visualizations of your models and data. 

TensorWatch is designed to be flexible and extensible so you can even build your own custom visualizations, UIs, and dashboards. Besides traditional what-you-see-is-what-you-log approach, it also has a unique capability to execute arbitrary query against your live ML training process, return a stream as a result of the query and view this stream using your choice of a visualizer (we call this [Lazy Logging Mode](#lazy-logging-mode])).

TensorWatch is under heavy development with a goal of providing a platform for debugging machine learning in one easy to use, extensible, and hackable package.

<img src="docs/images/teaser.gif" alt="TensorWatch in Jupyter Notebook" width="400"/>

## How to Get It

```
pip install tensorwatch
```

TensorWatch supports Python 3.x and is tested with PyTorch 0.4-1.x. Most features should also work with TensorFlow eager tensors.

## How to Use It

### Quick Start

Here's simple code that logs an integer and its square as a tuple every second to TensorWatch:
```
import tensorwatch as tw
import time

# streams will be stored in test.log file
w = tw.Watcher(filename='test.log')

# create a stream for logging
s = w.create_stream(name='metric1')

# generate Jupyter Notebook to view real-time streams
w.make_notebook()

for i in range(1000):
    # write x,y pair we want to log
    s.write((i, i*i)) 

    time.sleep(1)
```
When you run this code, you will notice a Jupyter Notebook file `test.ipynb` gets created in your script folder. From command prompt type `jupyter notebook` and click on `test.ipynb`. Chose *Cell > Run all* in the menu to see real-time line graph as values get written in your script.

Here's the output you will see in Jupyter Notebook:

<img src="docs/images/quick_start.gif" alt="TensorWatch in Jupyter Notebook" width="250"/>

To dive deeper in to various other features, please see [Tutorials](#tutorials) and [notebooks](https://github.com/microsoft/tensorwatch/tree/master/notebooks).

### How does this work?

When you write to TensorWatch stream, the values get serialized and sent to TCP/IP socket as well as the file you specified. From Jupyter Notebook, we load previously logged values from the file and then listen to TCP/IP socket for any future values. The visualier listens to the stream and renders values as it arrives.

Ok, so that's a bit of a very simplified description. TensorWatch architecture is actually much more powerful. Almost everything in TensorWatch is a *stream*. Files, sockets, console and even visualizers themselves are stream. A cool thing about TensorWatch streams is that they can listen to other streams. This allows TensorWatch to create a *data flow graph*. This means a visualizer can listen to many streams simultaneously, each of which could be file, socket or some other stream. You can recursively extend this to build arbitrary data flow graphs. TensorWatch decouples streams from how they get stored and how they get visualized.


## Visualizations
In the above example, the line graph is used as the default visualization. However, TensorWatch supports many other visualizations including histogram, pie charts, scatter charts, bar charts and 3D versions of many of these plots. You can log your data, specify the chart type you want and let TensorWatch take care of the rest. 

One of the significant strengths of TensorWatch is to be able to combine, compose, and create custom visualizations effortlessly. For example, you can choose an arbitrary number of streams to get visualized in the same plot. Or you can have the same stream get visualized in many different plots  *simultaneously*. Or you can place an arbitrary set of visualizations side-by-side. Or you can create your own custom visualization simply by creating a new Python class that implements few methods.

## Comparing Results of Multiple Runs
Each TensorWatch stream may contain a metric of your choice. By default, TensorWatch saves all streams in a single file, but you could also choose to save each stream in seprate file or not save at all (for example, stream over sockets or in console, zero hit to disk!). Later you can open these streams and direct them to one or more visualizations. This design allows you to quickly compare the results from your different experiments in your choice of visualizations easily. 

## Training within Jupyter Notebook
Many times you might prefer to do data analysis, ML training, and testing - all from within Jupyter Notebook instead of from a separate script. TensorWatch can help you do sophisticated real-time visualizations effortlessly from code that runs within Jupyter Notebook end-to-end.

## Lazy Logging Mode
An unique feature in TensorWatch is the ability to query the live running process, retrieve the result of this query as a stream and direct this stream to your preferred visualization(s). You don't need to log any data beforehand. We call this new way of debugging and visualization a *lazy logging mode*.

For example, below, we show input and output image pairs randomly sampled during the training of an autoencoder on fruits dataset. These images were not logged beforehand in the script. Instead, the user sends query as Python lambda expression which results in a stream of images that gets displayed in Jupyter Notebook:

<img src="docs/images/fruits.gif" alt="TensorWatch in Jupyter Notebook" width="200"/>

See [Lazy Logging Tutorial](https://github.com/microsoft/tensorwatch/blob/master/docs/lazy_logging.md).

### Pre-Training and Post-Training Tasks

TensorWatch leverages several excellent libraries including [hiddenlayer](https://github.com/waleedka/hiddenlayer), [torchstat](https://github.com/Swall0w/torchstat), [Visual Attribution](https://github.com/yulongwang12/visual-attribution) to allow performing usual debugging and analysis activities in one consistent package and interface.

For example, you can view the model graph with tensor shapes with one liner:

<img src="docs/images/draw_model.png" alt="Model graph for Alexnet" width="400"/>

You can view statistics for different layers such as flops, number of parameters etc:

<img src="docs/images/model_stats.png" alt="Model statistics for Alexnet" width="600"/>

[See notebook](https://github.com/microsoft/tensorwatch/blob/master/notebooks/network_arch.ipynb).

You can view dataset in lower dimensional space using techniques such as t-sne:

<img src="docs/images/tsne.gif" alt="t-sne visualization for MNIST" width="400"/>

[See notebook](https://github.com/microsoft/tensorwatch/blob/master/notebooks/data_exploration.ipynb).

### Prediction Explanations
We wish to provide various tools for explaining predictions to help debug models. Currently, we offer several explainers for convolutional networks, including [Lime](https://github.com/marcotcr/lime). For example, below highlights the areas that cause Resnet50 model to make a prediction for class 240 for Imagenet dataset:

<img src="docs/images/saliency.png" alt="CNN prediction explanation" width="300"/>

[See notebook](https://github.com/microsoft/tensorwatch/blob/master/notebooks/cnn_pred_explain.ipynb).


## Tutorials

- [Simple Logging Tutorial](https://github.com/microsoft/tensorwatch/blob/master/docs/simple_logging.md)

- [Lazy Logging Tutorial](https://github.com/microsoft/tensorwatch/blob/master/docs/lazy_logging.md)

## Contribute

We would love your contributions, feedback, questions, and feature requests! Please [file a Github issue](https://github.com/microsoft/tensorwatch/issues/new) or send us a pull request. Please review the [Microsoft Code of Conduct](https://opensource.microsoft.com/codeofconduct/) and [learn more](https://github.com/microsoft/tensorwatch/blob/master/CONTRIBUTING.md).

## Contact

Join the TensorWatch group on [Facebook](https://www.facebook.com/groups/378075159472803/) to stay up to date or ask any questions.

## Credits

TensorWatch utilizes several open source libraries for many of its features. These includes: [hiddenlayer](https://github.com/waleedka/hiddenlayer), [torchstat](https://github.com/Swall0w/torchstat), [Visual-Attribution](https://github.com/yulongwang12/visual-attribution), [pyzmq](https://github.com/zeromq/pyzmq), [receptivefield](https://github.com/fornaxai/receptivefield), [nbformat](https://github.com/jupyter/nbformat). Please see `install_requires` section in [setup.py](setup.py) for upto date list.

## License

This project is released under the MIT License. Please review the [License file](LICENSE.txt) for more details.
