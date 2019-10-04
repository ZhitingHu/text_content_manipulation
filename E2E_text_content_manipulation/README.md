# E2E Restaurant Dataset for Text Content Manipulation
This is the E2E restaurant dataset for the task of text content manipulation, as first proposed in the paper:  
[Toward Unsupervised Text Content Manipulation](https://arxiv.org/abs/1901.09501)
# Data Format  
Similar to NBA dataset, each example in the dataset consists of four elements, namely, `(x, y_aux, x_ref, y_ref)`, where  
+ `x` is a content record containing a set of data tuples `x = {x_i}`. Each tuple x_i contains three fields (type, value, associated).
 For example, `x_i = (priceRange, cheap, The_Eagle)` means _"The_Eagle has a cheap price range"_. More specifically,

  - type: data type of the tuple, `e.g., name, eatType, etc`. There are 8 data types in total. See the file x_type.vocab.txt for all data types.
  - value: value of the data. Usually a string (e.g., a restaurant's priceRange).
  - associated: the name of the associated restaurant .
  
  The above three fields of each `x` instance are stored in three parallel files, respectively. For example, each line in the file `train/x_type.train.txt` contains data types of all tuples in each `x` training instance. Data types are separated by white spaces. For example, the first line in `train/x_type.train.txt` is `name eatType food priceRange area`, meaning that there are 5 tuples in the first x instance, each of which has the respective type.

+ `y_aux` is the auxiliary sentence describing the content of `x`.

+ `x_ref` is the content record of reference sentence `y_ref`, in the same format as `x`. During data construction, we have guaranteed x_ref has the same number of tuples but some different types.The number setting of unpaired types is flexible, e.g.(0, 1 or 2), for the diversity of reference sentences.

+ `y_ref` is the reference sentence that defines the desired writing style of output sentence.

# Data Files
+ The dataset is split into train/val/test sets, each in corresponding folder, respectively.

+ The four elements `(x, y_aux, x_ref, y_ref)` of each example are stored in parallel files, respectively. For example, each line of `train/y_aux.train.txt` is an auxiliary sentence of the respective data example.

  As explained above, three fields of `x` are separately stored in three files, namely, (taking training data for example), `x_type.train.txt, x_value.train.txt,` and `x_associated.train.txt,` respectively. `x_ref` is stored in the same format, in files like `x_ref_type.train.txt`.

+ The vocabulary file `y.vocab.txt` contains all words that have occurred in `y_aux` and `y_ref`. `x_type.vocab.txt`, `x_value.vocab.txt`, and `x_associated.vocab.txt` are the vocabulary of the 'type', 'value', and 'associated' fields of both `x` and `x_ref`.

# Data Statics
|            |    train    |    valid    |    test    |
|    ------: |    :-------:    |    :---------   |    ------    |
|    #Instances    |    29,486     |    6,299    |    6,273    |
|    #Tokens    |    0.54M     |    0.12M    |   0.12M   |
|    Avg Sentence Length   |    18.36      |     18.34    |    18.35   |
|    #Data Types   |   8    |   8    |   8    |
|   Avg Record Length   |   5.38       |    5.38    |   5.35    |
			
# Dataset Creation Process

This dataset is derived from [The E2E Challenge Dataset](https://github.com/tuetschek/e2e-dataset) which was used in the [ E2E NLG Challenge](http://www.macs.hw.ac.uk/InteractionLab/E2E/). It is for training end-to-end, data-driven natural language generation systems in the restaurant domain.

In the E2E Generation dataset, the input is the semantics containing slots and their values, and the output is the corresponding natural language. To obtain our data:

+ We first process each record into tuples. Specifically, the above slot, value and the first value `name` constitute a data type,a value, and an associated entity respectively.  In this step, we make sure each value of the tuple become a single token (e.g., `Bibimbap_House, more_than_£30` ).

+ Meanwhile, the corresponding auxiliary sentence should do the same tokenization. For categorical values, like `no`, which cannot easily be aligned with a phrase, we simply replace them with the value ,like `not_family_friendly`, with the same meaning.So far, we have obtained all `(x, y_aux)` pairs.

+ To obtain the reference sentence `y'`, the coresponding retrieved record `x'` is designated to have the same number of tuples yet different data types. The number setting ,e.g.,(0, 1 or 2), of unpaired data types is flexible for the diverse pattern of reference sentences. 

