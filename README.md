# Wikipedia IDF Table Generator

A Python utility for generating an **Inverse Document Frequency (IDF) table** from Wikipedia article data stored as JSON or compressed `.bz2` files.

This project is based on the original [`wikipedia-idf`](https://github.com/marcocor/wikipedia-idf) implementation and has been modified to use **spaCy for tokenization** instead of NLTK and to remove stemming support.

## Features

* Processes Wikipedia articles from JSON files.
* Supports `.bz2` compressed input files.
* Uses **spaCy** for word tokenization.
* Converts tokens to lowercase.
* Removes tokens consisting only of non-word characters.
* Calculates token frequency across Wikipedia articles.
* Calculates IDF for every unique token.
* Supports multiple input files.
* Supports multiprocessing for faster processing.
* Allows limiting the number of processed articles.
* Generates a CSV file containing the IDF table.

## How It Works

The script expects each input file to contain JSON objects, one article per line.

For example:

```json
{"text": "Wikipedia is a free online encyclopedia."}
```

The processing pipeline is:

```text
Wikipedia JSON files
        ↓
Read article
        ↓
Extract "text"
        ↓
spaCy tokenization
        ↓
Remove unwanted tokens
        ↓
Convert tokens to lowercase
        ↓
Create unique token set per article
        ↓
Count document frequency
        ↓
Calculate IDF
        ↓
terms.csv
```

### IDF Calculation

For each token, the script calculates:

```text
IDF = log(Number of Articles / Document Frequency)
```

For example, if there are 10,000 articles and the word `computer` appears in 100 articles:

```text
IDF = log(10000 / 100)
    = log(100)
```

A word appearing in many articles receives a **lower IDF**, while a word appearing in fewer articles receives a **higher IDF**.

## Requirements

Python 3.8+ is recommended.

Install the required packages:

```bash
pip install spacy unicodecsv
```

No pre-trained spaCy model is required because the script creates a blank language pipeline:

```python
spacy.blank(args.spacy_lang)
```

For English, for example:

```python
spacy.blank("en")
```

## Input Format

The input must contain **one JSON object per line**.

Example:

```json
{"text": "Machine learning is a field of artificial intelligence."}
{"text": "Artificial intelligence is used in many applications."}
{"text": "Natural language processing works with human language."}
```

The script reads the `text` field from each JSON object:

```python
article_json["text"]
```

### Multiple Input Files

Multiple JSON files can be provided:

```bash
python generate_idf.py \
    -i wikipedia1.json wikipedia2.json wikipedia3.json \
    -s en \
    -o output
```

## Usage

Basic usage:

```bash
python generate_idf.py -i input.json -s en -o output
```

### Arguments

| Argument             | Required | Description                             |
| -------------------- | -------- | --------------------------------------- |
| `-i`, `--input`      | Yes      | One or more input JSON/`.bz2` files     |
| `-s`, `--spacy_lang` | Yes      | spaCy language code, such as `en`       |
| `-o`, `--output`     | Yes      | Base name/path for the output file      |
| `-l`, `--limit`      | No       | Maximum number of articles to process   |
| `-c`, `--cpus`       | No       | Number of CPU processes; default is `1` |

## Examples

### Process an English Wikipedia dump

```bash
python generate_idf.py \
    -i wikipedia.json \
    -s en \
    -o wikipedia_idf
```

The output will be:

```text
wikipedia_idf_terms.csv
```

### Process only 10,000 articles

```bash
python generate_idf.py \
    -i wikipedia.json \
    -s en \
    -o wikipedia_idf \
    -l 10000
```

### Use multiple CPU processes

```bash
python generate_idf.py \
    -i wikipedia.json \
    -s en \
    -o wikipedia_idf \
    -c 8
```

### Process compressed `.bz2` files

The script automatically detects files ending in `.bz2`:

```bash
python generate_idf.py \
    -i wikipedia.json.bz2 \
    -s en \
    -o wikipedia_idf_
```
