# Access to Data


## Ethics

(downloading)=
## Downloading the dataset

[DataLad](https://www.datalad.org/) is a tool for versioning a large data structure in a git repository. The dataset can be explored without downloading the data, and it is easy to only download the subset of the data you need for your project.
See the [DataLad handbook](http://handbook.datalad.org/en/latest/) for further information.

We recommend creating an SSH key (if not already present) on the machine on which the dataset will be installed and adding it to github. See the official [github instructions](https://help.github.com/en/enterprise/2.15/user/articles/adding-a-new-ssh-key-to-your-github-account) on how to create and add a key to your account.

To obtain the data, you need to install a recent version of the [DataLad software](http://handbook.datalad.org/en/latest/intro/installation.html), available for Linux, OSX and Windows. Note that you need to have valid login credentials to access the NeuroMod git as well as the NeuroMod [Amazon S3](https://aws.amazon.com/s3) fileserver. Once you have obtained these credentials, you can proceed as follows in a terminal:
```
# Install recursively the dataset and subdataset of the current project.
# If using ssh git clone as follow, you can set your public SSH key in the present git to ease future updates.
datalad install -r git@github.com:courtois-neuromod/cneuromod.git
# If errors show up relative to .heudiconv subdataset/submodule, this is OK, they are not published (will be cleaned up in the future).
cd cneuromod
```

### Versioning

By default, this will install the latest stable release of the dataset, which is the recommended version to get for a new analysis.
If you are need to work on a specific version (for instance to reproduce a result), you can change to the appropriate tag with.
```
git checkout 2020
```

We now set as environment variable the credentials to the file server. The s3 access_key and secret_key will be provided by the data manager after being granted access to cneuromod by the user access committee.
```
# This needs to be set in your `bash` everytime you want to download data.
export AWS_ACCESS_KEY_ID=<s3_access_key>  AWS_SECRET_ACCESS_KEY=<s3_secret_key>
```

### Preprocessed data

For analysis of fMRI data, it is preferable to directly get the preprocessed data (smriprep and fmriprep for now).

```
datalad install git@github.com:courtois-neuromod/cneuromod.processed.git
cd cneuromod.processed
```

You can install the sub-datasets you are interested in (instead of installing all of them) using for instance:
```
datalad get -n smriprep fmriprep/movie10
```
and then get only the files you need (for instance MNI space output):
```
datalad get smriprep/sub-*/anat/*space-MNI152NLin2009cAsym_* # get all anatomical output in MNI space
datalad get fmriprep/movie10/sub-*/ses-*/func/*space-MNI152NLin2009cAsym_* # get all functional output in MNI space
```
You can add the flag `-J n` to download files in parallel with `n` being the number of threads to use.

The source data used for preprocessing (including raw data) are referenced as sources in the preprocessed dataset following [Yoda](https://handbook.datalad.org/en/latest/basics/101-127-yoda.html), so as to track provenance.
You can also track the version of the cneuromod dataset you are using by installing it in a datalad dataset created for your project.


### Stimuli and event files

You will likely need the events files and stimuli for your analysis which can be obtained from the sourcedata reference sub-datasets, for example:
```
datalad get -r fmriprep/movie10/sourcedata/movie10/stimuli fmriprep/movie10/sourcedata/movie10/*_events.tsv
```

or to get subject specific event files for tasks collecting behavioral responses:
```
datalad get -r fmriprep/movie10/sourcedata/hcptrt/sub-*/ses-*/func/*_events.tsv
```

## Updates

The dataset will be updated with new releases so you might want to get these changes (unless you are currently running analyses, or trying to reproduce results). The main branches of all datasets will always track the latest stable release.

```
# update the dataset recursively
datalad update -r --merge --reobtain-data

```
Once your local dataset clone is updated, you might need to pull new data, as some files could have been added or modified. The `--reobtain-data` flag should automatically pull files that you had already downloaded in case these were modified.
