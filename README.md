# Phishing URL Detection

Given a URL string, predict how likely it is to be a phishing site. The model
reads the URL only. It never fetches the page and never queries WHOIS or DNS.

Work in progress. No metrics yet.

## Approach

Features come from the URL string alone, so prediction is fast and runs offline.

Splits are grouped by registered domain instead of random, so the model cannot
score well by memorising domains it has already seen.

The decision threshold is tuned to maximise recall while keeping the false
positive rate at or below 1%. Blocking a legitimate site costs more than
missing a phishing one.

Part of the project is auditing the PhiUSIIL dataset, where the usual ~99.9%
accuracy comes from label leakage and content features that do not exist at
prediction time.

## Setup

Python 3.13.7.

```bash
python3 -m venv .venv
source .venv/bin/activate        
pip install -r requirements.txt
```

## Getting the data

The dataset is not committed. There are two ways to fetch it.

Manually: open the
[PhiUSIIL Phishing URL Dataset](https://www.kaggle.com/datasets/ndarvind/phiusiil-phishing-url-dataset)
page, click Download, unzip it, and move the CSV into `data/raw/`.

Or with the Kaggle CLI, which is reproducible but needs an API token. Get the
token from your Kaggle account under Settings, API, Create New API Token. It
downloads as `kaggle.json`.

```bash
pip install kaggle
mkdir -p ~/.kaggle && mv ~/Downloads/kaggle.json ~/.kaggle/
chmod 600 ~/.kaggle/kaggle.json
kaggle datasets download -d ndarvind/phiusiil-phishing-url-dataset -p data/raw --unzip
```

`kaggle` is deliberately not in `requirements.txt`. You need it once to get the
data, not to run anything in the project.

## Layout

```
data/         raw, interim and processed data (gitignored)
notebooks/    exploration and figures
src/          features, split, train, evaluate, predict
tests/        pytest suite for the feature extractor
models/       serialised model and model card
reports/      figures
app/          Streamlit demo
```

## Limitations

URL structure only, so phishing that depends on page content is invisible to
it. Hardcoded lists of suspicious TLDs and brand names will go stale. Portfolio
project, not a security control.
