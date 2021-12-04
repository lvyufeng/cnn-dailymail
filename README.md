This code produces the non-anonymized version of the CNN / Daily Mail summarization dataset, as used in the ACL 2017 paper *[Get To The Point: Summarization with Pointer-Generator Networks](https://arxiv.org/pdf/1704.04368.pdf)*. It processes the dataset into the binary format for the **MindSpore** model instead of Tensorflow.

# Option 1: Download the processed data

## Download from Baidu Netdisk

- All preprocessed files: [Download URL](https://pan.baidu.com/s/1pMJgVSwrCXhsHSn_9MNFbQ), Password: **rmms**

## Download from Google Drive
- CNN_STORIES_TOKENIZED: [Download CNN DATASETS](https://drive.google.com/file/d/13r2gB2uiQuDcynvcfX8OO1MpGl5U2ane/view?usp=sharing)
- DM_STORIES_TOKENIZED: [Download DM DATASETS](https://drive.google.com/file/d/1kAxPcDjx-jXErjmiFKReDthoVLX3o596/view?usp=sharing)
- FINISHED_FILES: ===> [Download FINISHED FILES](https://drive.google.com/file/d/1FERskXzpsExwXUAbXkde0Fd9nAmqICwX/view?usp=sharing)

# Option 2: Process the data yourself

## 1. Download data

Download and unzip the `stories` directories from [here](http://cs.nyu.edu/~kcho/DMQA/) for both CNN and Daily Mail. 

**Warning:** These files contain a few (114, in a dataset of over 300, 000) examples for which the article text is missing - see for example `cnn/stories/72aba2f58178f2d19d3fae89d5f3e9a4686bc4bb.story` . 

## 2. Download Stanford CoreNLP

We will need Stanford CoreNLP to tokenize the data. Download it [here](https://stanfordnlp.github.io/CoreNLP/) and unzip it. Then add the following command to your bash_profile:

```bash
export CLASSPATH=/path/to/stanford-corenlp-4.3.2/stanford-corenlp-4.3.2.jar
```

replacing `/path/to/` with the path to where you saved the `stanford-corenlp-4.3.2` directory. You can check if it's working by running

```bash
echo "Please tokenize this text." | java edu.stanford.nlp.process.PTBTokenizer
```

You should see something like:

```
Please
tokenize
this
text
.
PTBTokenizer tokenized 5 tokens at 68.97 tokens per second.
```

## 3. Process into .bin and vocab files

Run

```bash
python make_datafiles.py [/path/to/cnn/stories] [/path/to/dailymail/stories] [/path/to/save]
```

replacing `/path/to/cnn/stories` with the path to where you saved the `cnn/stories` directory that you downloaded; similarly for `dailymail/stories` .

This script will do several things:
* The directories `cnn_stories_tokenized` and `dm_stories_tokenized` will be created and filled with tokenized versions of `cnn/stories` and `dailymail/stories`. This may take some time. ***Note**: you may see several `Untokenizable:` warnings from Stanford Tokenizer. These seem to be related to Unicode characters in the data; so far it seems OK to ignore them.*
* For each of the url lists `all_train.txt`,  `all_val.txt` and `all_test.txt`, the corresponding tokenized stories are read from file, lowercased and written to serialized binary files `train.bin`,  `val.bin` and `test.bin`. These will be placed in the newly-created `finished_files` directory. This may take some time.
* Additionally, a `vocab` file is created from the training data. This is also placed in `finished_files`.
* Lastly, `train.mindrecord`,  `val.mindrecord` and `test.mindrecord` will be split into chunks of 20000 examples per chunk. These chunked files will be saved in `finished_files/chunked` as e.g. `train_000.bin`, `train.mindrecord0`, ..., `train.mindrecord14`.
