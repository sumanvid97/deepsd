# DeepSD: Generating High Resolution Climate Change Projections through Single Image Super-Resolution

The code in this repository contains the Stacked Super-resolution CNN proposed in our recent KDD paper, [DeepSD](http://www.kdd.org/kdd2017/papers/view/deepsd-generating-high-resolution-climate-change-projections-through-single). 

When cloning the repository, make sure to use the `--recursive` argument as DeepSD directly relys on our [SRCNN library](https://github.com/tjvandal/srcnn-tensorflow). Ie. `git clone --recursive https://github.com/tjvandal/deepsd.git`. 

### SRCNN with AUX Var Network Architecture
![Alt text](deepsd-network.png?raw=true)

### DeepSD is a Stack of SRCNNs w/ AUX var
![Alt text](deepsdflow.png?raw=true)

## Dependencies

The current codebase has only been tested with Python2, not Python3. Major dependencies include Tensorflow and Gdal libraries.
The user must install these independently of this package.  I will try to add more support regarding dependencies at a later time.

## Usage

### Quick Look

To run the example code execute the following on the terminal: <br>
`python prism.py` <br>
`bash run_job.sh` <br>
`python inference.py` <br>

### Configuration File

`config.ini` provides and example configuration file allowing one to selection prism data options, file directories, architecture selection, and how many models to stack. All of the following scripts which download and process the data, train the models, and do inference, rely directly on this configuration file.  One can make their own config file and include it as an argument to the scripts.

### Download and Process Data 
 
`prism.py` -- To simpilify the example, I download year 2014 for training and 2015 for testing (as set in `config.ini`), but given the high resolution, the data size is still a couple gbs. Training data is processed by selecting sub-images of size 38x38 and saved to a tfrecords file. The test set contains full prism precipitation maps and saved to corresponding tfrecord files.
 
### Train Model

`train.py` -- This file handles the heavy lifting for reading in the data, building the graph, and learning the parameters. The `--model_number` parameter allows one to select which configured model to train, ie. what resolutions and data. Tensorboard summary files will be saved inside the configured scratch directory. The checkpoints are saved in the scratch directory which contain all then necessary information for inference.  

`run_job.sh` -- Edit this file to use a single script to train multiple models.  Our example provides trains just two SRCNN networks.

### Inference

`inference.py` -- To downscale we need to join all the trained models to a single graph. This script loops through all the models in `config.py` to freeze, join, and apply inference. This script can easily be editted to include GCM outputs. 
