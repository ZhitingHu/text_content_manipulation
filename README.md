# Paired NBA Record-Sentence Dataset

This dataset is first proposed and made in [Toward Unsupervised Text Content Manipulation](https://arxiv.org/abs/1901.09501) (Wang, Hu, et al.).

## Format

The dataset is split into train/valid/test sets, each consists of many paired examples (x, y, x', y'), where x is a set of records describing the sentence y, and x' is a set of records describing the sentence y'. x, y, x', y' are separated in files with prefixes `x`, `y`, `x_ref`, `y_ref`, respectively. A record in x or x' is a triple (entry, attribute, value), e.g., (25, TEAM-AST, Boston) means "Boston had 25 assists". x is separated into files with prefixes `x_entry`, `x_attribute`, `x_value`, respectively, while x' is separated into files with prefixes `x_ref_entry`, `x_ref_attribute`, `x_ref_value`, respectively. Each of entry, attribute and value has its  vocabulary collected in `*.vocab.txt`. All tokens are collected into a single vocabulary `all.vocab.txt`.

Each paired example (x, y, x', y') meets: x, x' are "very close". By "very close" we means that the set of the attributes in them are almost but not perfectly match. See the creation of the dataset for more details.

##Creation

This dataset is made from one of the [Data-to-Text Datasets](https://github.com/harvardnlp/boxscore-data) proposed in [Challenges in Data-to-Document Generation](https://arxiv.org/abs/1707.08052) (Wiseman, Shieber, Rush; EMNLP 2017). You can download it from [here](https://github.com/harvardnlp/boxscore-data/blob/master/rotowire.tar.bz2?raw=true). See [harvardnlp/boxscore-data](https://github.com/harvardnlp/boxscore-data) for details.

The original dataset is then preprocessed with a modified version of [the script](https://github.com/harvardnlp/data2text/blob/master/data_utils.py) from original [harvardnlp/data2text](https://github.com/harvardnlp/data2text). During this process, all entity (team/city/player) names are extracted and merged into single tokens, and all (English or digital) numbers are extracted and replaced by their digital forms. Now we have obtained cleaned sentences, i.e. ys.

Then we also use the script to extract all candidate relations between the entities and numbers extracted in each sentence y. More rule-based constraints are imposed to filter out redundant relations as much as possible. These extracted relations forms x. So far, we have obtained all (x, y) for each of the train/valid/test sets.

Finally, for every (x, y) in the train/valid/test sets, <a href="https://www.codecogs.com/eqnedit.php?latex=(x',&space;y')&space;=&space;\arg\min_{(x'',&space;y'')&space;\in&space;\text{train&space;set}&space;\mid&space;J(x,&space;x'')&space;\neq&space;1}&space;|J(x,&space;x'')&space;-&space;1|" target="_blank"><img src="https://latex.codecogs.com/gif.latex?(x',&space;y')&space;=&space;\arg\min_{(x'',&space;y'')&space;\in&space;\text{train&space;set}&space;\mid&space;J(x,&space;x'')&space;\neq&space;1}&space;|J(x,&space;x'')&space;-&space;1|" title="(x', y') = \arg\min_{(x'', y'') \in \text{train set} \mid J(x, x'') \neq 1} |J(x, x'') - 1|" /></a>, where J(A, B) is the Jaccard index between two sets A and B.

## Statistics

|                     | train | valid | test  |
| ------------------- | ----- | ----- | ----- |
| \#Instances         | 31751 | 6833  | 6999  |
| \#Tokens            | 1.64M | 0.35M | 0.36M |
| Avg Sentence Length | 25.90 | 25.87 | 25.99 |
| \#Data Types        | 34    | 34    | 34    |
| Avg Record Length   | 4.88  | 4.88  | 4.94  |

