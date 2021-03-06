# prepare-large-datasets
Process wikipedia or other large corpuses and create a dataset for NLP applications.

# How to (Wikipedia)

## Download the data

Download a specific wikipedia dump:
```
./download_dump.sh it
```

Download dumps for all languages used by BERT:

```bash
for i in $(cat required_languages.tsv| cut -f2); do ./download_dump.sh $i; done
```


## Extract the data

This will create another file with the txt extension containing 1 file per line:
```bash
./extract_and_clean_wiki_dump.sh data/enwiki-latest-pages-articles.xml.bz2
```


## Pre-process the data

Preprocess data and save in a file with the same name and `_preprocessed` suffix. Be sure to install the `blingfire` tokenizer:

```bash
pip install blingfire
python preprocess_wiki_dump.py data/enwiki-latest-pages-articles.txt
```

## Create the dataset (Wikipedia & Others)

### Monolingual

You can pass the name of a tokenizer from the `huggingface` library to create lines that are filled with `<target_len>` tokens you require. For example, with `--target_len 128` it will fill up to 128 tokens per line based on the specified tokenizer. This will occupay all the available CPUs on your machine and may take some time if tokenization is enabled (by passing `--fill_for_tokenizer <pre-trained-tok-name>`)

```bash
pip install transformes
python create_dataset.py -i data/enwiki-latest-pages-articles_preprocessed.txt -o data/enwiki-latest-pages-articles_preprocessed_dense_bert_128.tsv --fill_for_tokenizer bert-base-cased -f --target_len 128
```

### Multilingual

You can create a multilingual dataset by passing the lang_file and multiple input files.

```bash
python create_dataset.py -i data/*_preprocessed.txt -o data/multilingual_dataset.tsv --fill_for_tokenizer bert-base-multilingual-cased -f --target_len 128 --lang_file lang_dict.json
```

Each line will contain an additional id of the language. `ids` are store in `lang_dict.json`.


# Credits

Most of the `sh` scripts has been taken by [Steven van de Graaf article](https://towardsdatascience.com/pre-processing-a-wikipedia-dump-for-nlp-model-training-a-write-up-3b9176fdf67)

Thanks also to the user `attardi` for creating the [wikiextractor repository](https://github.com/attardi/wikiextractor)
