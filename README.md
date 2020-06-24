# scanem 

This GitHub repository contains the files needed and instructions to run the Nextflow pipeline of **scanem**.

<img src="https://github.com/jacobhepkema/scanem/raw/master/scanem_logo.png" width=300 align=right>

What is **scanem**? **scanem** is a convolutional neural network (CNN) for inference of the relevant *cis*-regulatory motifs for certain gene lists by training on single-cell RNA-seq data. It finds weights for these motifs (weighing their 'impact'). For more information, see [link](#).

The network is written in pytorch, with the downstream analyses written in R (using ggplot for plotting). Running the network and running the downstream analysis is implemented in a Nextflow pipeline. 

## Analysing model output

See [this RMarkdown script](#AddLink) for a walk-through for analysing the output of the network using R. 

## Features

UPDATE with examples

## How to use

On the command line, clone this repository into your current working directory with the following command:
```
git clone https://github.com/jacobhepkema/scanem_pytorch
```
Alternatively, download the repository directly and place in a folder of your choice. 
`cd` into the top directory (`/scanem`) to run **scanem**.

Usage:
```
nextflow run [options] scanem.nf [arg...]
```

Example command:
```
nextflow run -profile singularity scanem.nf \
  --name test_run /
  --data /data/mock_data.tsv \
  --num_calibrations 30 \
  --val_factor 10 \
  --epochs 500 \
  --num_errtest 10 \
  --num_motifs 64 \
  --motif_length 12
```

Minimal command:
```
nextflow run scanem.nf \
  --name test_run \
  --data /data/input_data.tsv 
```

**Options**:

`-profile`
Choose a specified profile configuration from the `/conf` directory. Options:
`-profile singularity` 

`-with-report name.html`
Generates a Nextflow report webpage with information on task run times, CPU usage, memory usage, and I/O. Note that this does _not_ include information on GPU usage.

<!-- Options include `local` (default option), `lsf`, and `docker`. The `lsf` profile  uses a [Singularity](https://sylabs.io/docs/) image (built from [this git repo](https://github.com/jacobhepkema/scanem-wip) using [Singularity Hub](https://singularity-hub.org/)). For more options, see the [Nextflow documentation](https://www.nextflow.io/docs/latest/index.html) or run `nextflow run -h`. The cache directory for the Singularity image can be set in `/conf/lsf.config` by setting `cacheDir = ...` -->

**Arguments**:

`--name`
The name of the current experiment. Default value: `experiment_001`

`--data`
The path to the dataset (relative to `SCANEM.nf`) to train the network. Add your data in the example tab-separated format into the `/data` folder. Default value: `/data/input_data.tsv`.

`--num_calibrations`
The amount of randomly intialized calibrations. Default value: `30`

`--num_candidates`
The amount of candidate models with optimal initial parameters that should be run. Default value: `10`

`--val_factor`
Sets the K in K-fold cross-validation. Default value: `10`.

`--epochs`
Sets the amount of epochs (1 epoch = 1 forward and backward cycle through the entire training set) the network goes through. Default value: `100`

`--num_errtest`
After `num_errtest` learning steps/batches, the network will assess validation error and saves all network parameters for that training 'time point'. These parameters can be found in the network output HDF5 file. *Note: make sure that `num_learnsteps` divided by `num_errtest` is at least 2*. Default value: `10`

`--batch_size`
The size of one training batch (amount of sequences and the corresponding outputs). After one batch, the network will update its parameters through back-propagation. Default value: `32`

`--alpha`
Dropout expected value (parameter of the Bern(alpha) distributed masks). When set to 1.0, this amounts to no dropout. Default value: 1.0.

`--motif_length`
The length of each individual regulatory motif. Default value: .

`--motif_amount`
The amount of regulatory motifs to look for. Default value: .

`--sigma_motifs_min`
UPDATE Default value: 

`--sigma_motifs_max`
UPDATE Default value: 

`--sigma_net_min`
UPDATE Default value: 

`--sigma_net_max`
UPDATE Default value: 

`--epsilon_min`
UPDATE Default value: 

`--epsilon_max`
UPDATE Default value: 

`--opt`
The pytorch optimizer to use. Options include `SGD` and `Adam`

`--cluster_of_motifs_file`
UPDATE The path to the `cluster_of_motifs.txt` file. Default value: `/resources/cluster_of_motifs.txt`.

## Dependencies

Install [Nextflow](https://www.nextflow.io/docs/latest/getstarted.html#installation) by following the instructions on [their website](https://www.nextflow.io/docs/latest/getstarted.html#installation). 
Move the nextflow file to a directory accessible by your `$PATH` variable so that nextflow can be run by typing `nextflow` rather than the full path to the executable. One way of doing this is with the following line:
```
export PATH="path/to/nextflow:$PATH"
```
Alternatively, manually add `export PATH="path/to/nextflow:$PATH"` to your `~/.bashrc` or `~/.bash_profile` file, depending on which file is used as a source for the command line environment. 

When running locally (in non-singularity mode), the following dependencies are required:
```
python 3.6 or above

UPDATE

R ...
```

<!--
## Included files

```
.
├── bin
│   ├── scanem_downstream_analysis.r
│   ├── scanem_functions.r
│   ├── scanem_model.py
│   ├── scanem_run.py
│   └── scanem_utils.py
├── conf
│   ├── singularity.config
│   └── singularity_nopy.config
├── data
│   └── mock_data.tsv
├── data_generation
│   ├── create_dataset.r
│   ├── genelist.txt
│   ├── hg38_500bp_promoters.fa.gz
│   ├── mm10_500bp_promoters.fa.gz
│   └── README.md
├── LICENSE
├── nextflow.config
├── output
├── README.md
├── resources
│   ├── cluster_of_motifs.txt
│   ├── Homo_sapiens.meme
│   ├── JASPAR2020_CORE_vertebrates_nr_pfms_meme.txt
│   └── Mus_musculus.meme
├── scanem_logo.png
├── scanem.nf
└── scanem_workflow.png
```

-->

## Example output
```
N E X T F L O W  ~  version 20.04.1
Launching `scanem.nf` [berserk_swirles] - revision: bb8afb69fd
=========================================================================
=========================================================================

  scanem  v0.1 

=========================================================================

  run name             : example_dataset
  data path            : data/example_dataset.tsv.gz
  cell label data path : data/example_dataset_celltypes.tsv
  motif length         : 12
  amount of motifs     : 300
  epochs               : 30
  batch size           : 128
  K in K-fold CV       : 10
  number of cal        : 30 
  number of candidates : 10
  tomtom db file       : resources/Mus_musculus.meme  
  random seed          : 42     

=========================================================================
=========================================================================

         
executor >  lsf (1)
[62/3eb656] process > train_scanem       [  0%] 0 of 1
[-        ] process > tomtom             -
[-        ] process > tomtom_allmotifs   -
[-        ] process > motif_analysis     -
```

## Running without Nextflow

Running without Nextflow is also possible, but this requires some more manual work. 
TODO add


## Questions and errors
If you have any questions, or want to report an error, please use our [github issues page](https://github.com/jacobhepkema/scanem_pytorch/issues)


## License
