# NBA Game Dataset for Text Content Manipulation

This is a dataset for the task of text content manipulation, as first proposed in the paper:

[Toward Unsupervised Text Content Manipulation](https://arxiv.org/abs/1901.09501)  
Wentao Wang*, Zhiting Hu*, Zichao Yang, Haoran Shi, Frank Xu, Eric P. Xing; 2019

## Data Format

Each example in the dataset consists of four elements, namely, `(x, y_aux, x_ref, y_ref)`, where

* `x` is a content record containing a set of data tuples `x = {x_i}`. Each tuple `x_i` contains three fields `(type, value, associated)`. For example, `x_i = (TEAM-AST, 25, Boston)` means *"The Boston got 25 team assists"*. More specifically,

    - `type`: data type of the tuple, e.g., `TEAM-AST`, `PLAYER-PTS`, etc. There are 34 data types in total. See the file [x_type.vocab.txt](x_type.vocab.txt) for all data types.
    - `value`: value of the data. Usually a scalar number or a string (e.g., a player's name).
    - `associated`: the associated team or player of the tuple. 
    
    The above three fields of each `x` instance are stored in three parallel files, respectively. For example, each line in the file `train/x_type.train.txt` contains data types of all tuples in each `x` training instance. Data types are separated by white spaces. For example, the first line in `train/x_type.train.txt` is `TEAM_NAME TEAM-AST TEAM-AST TEAM_NAME`, meaning that there are 4 tuples in the first `x` instance, each of which has the respective type.
    
* `y_aux` is the auxiliary sentence describing the content of `x`. 

* `x_ref` is the content record of reference sentence `y_ref`, in the same format as `x`. During data construction, we have guaranteed `x_ref` has a similar structure with `x`, but has a different number of tuples or has different values or types.

* `y_ref` is the reference sentence that defines the desired writing style of output sentence.

## Data Files

* The dataset is split into train/val/test sets, each in corresponding folder, respectively.

* The four elements `(x, y_aux, x_ref, y_ref)` of each example are stored in parallel files, respectively. For example, each line of `train/y_aux.train.txt` is an auxiliary sentence of the respective data example. 

  As explained above, three fields of `x` are separately stored in three files, namely, (taking training data for example), `x_type.train.txt`, `x_value.train.txt`, and `x_associated.train.txt`, respectively. `x_ref` is stored in the same format, in files like `x_ref_type.train.txt`.

* The vocabulary file `all.vocab.txt` contains all words that have occurred in the dataset. `x_type.vocab.txt`, `x_value.vocab.txt`, and `x_associated.vocab.txt` are the vocabulary of the 'type', 'value', and 'associated' fields of both `x` and `x_ref`.


## Data Statistics

|                     | train | valid | test  |
| ------------------- | ----- | ----- | ----- |
| \#Instances         | 31,751 | 6,833  | 6,999  |
| \#Tokens            | 1.64M | 0.35M | 0.36M |
| Avg Sentence Length | 25.90 | 25.87 | 25.99 |
| \#Data Types        | 34    | 34    | 34    |
| Avg Record Length   | 4.88  | 4.88  | 4.94  |


## Dataset Creation Process

We briefly describe the process of creating the above dataset.

This dataset is derived from one of the [Data-to-Text Datasets](https://github.com/harvardnlp/boxscore-data) (RotoWire) proposed in the paper [(Wiseman et al., 2017) Challenges in Data-to-Document Generation](https://arxiv.org/abs/1707.08052), which is for NBA game report generation. The original data can be downloaded from [here](https://github.com/harvardnlp/boxscore-data/blob/master/rotowire.tar.bz2?raw=true). 

The original dataset consists of (table, paragraph) pairs. We first split each data example into (record, sentence) pairs:

* The original dataset is then preprocessed with a modified version of [the script](https://github.com/harvardnlp/data2text/blob/master/data_utils.py) provided in the Data-to-Text dataset. In this step, we make sure each name of an entity (team/city/player) become a single token (e.g., `LeBron_James`, `Los_Angeles_Clippers`), and all numbers are replaced by their digital forms (e.g., if the original text is `fifty`, we replace it with `50`).

* We split the paragraph in each data example into sentences, i.e., the `y_aux`.

* We then use the above script to extract all candidate relations between entities and numbers in each sentence `y_aux`. More rule-based constraints are imposed to filter out as many redundant relations as possible. These extracted relations forms the record `x`. So far, we have obtained all `(x, y_aux)` pairs.

We next use a retrieval method to retrieve from the *training* set a `(x_ref, y_ref)` pair for each of the above `(x, y_aux)` pairs. In particular, as mentioned above, we want to guarantee `x_ref` has a similar but not exact the same content with `x`. Formally, we use the following criteria for retrieval:

<a href="https://www.codecogs.com/eqnedit.php?latex=(x_{ref},&space;y_{ref})&space;=&space;\arg\max_{(\bar{x},&space;\bar{y})&space;\in&space;\text{train&space;set}\&space;\&\&space;J(types(x),&space;types(\bar{x}))&space;<&space;1}&space;J(types(x),&space;types(\bar{x}))" target="_blank"><img src="https://latex.codecogs.com/gif.latex?(x_{ref},&space;y_{ref})&space;=&space;\arg\max_{(\bar{x},&space;\bar{y})&space;\in&space;\text{train&space;set}\&space;\&\&space;J(types(x),&space;types(\bar{x}))&space;<&space;1}&space;J(types(x),&space;types(\bar{x}))" title="(x_{ref}, y_{ref}) = \arg\max_{(\bar{x}, \bar{y}) \in \text{train set}\ \&\ J(types(x), types(\bar{x})) < 1} J(types(x), types(\bar{x}))" /></a>

where `types(x)` is the set of all data types in record `x`; `J(A, B)` is the Jaccard index between two sets A and B. The larger `J(A, B)` is, the closer A and B are. When `J(A, B) = 1`, A is exactly the same as B, otherwise there is some difference between them. We measure similarity between two records based on their types. Therefore, our criteria find `x_ref` that is most similar to but not exactly the same with `x`.
