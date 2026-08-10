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
source .venv/bin/activate        # fish: source .venv/bin/activate.fish
pip install -r requirements.txt
```

The dataset is not committed. Download the
[PhiUSIIL Phishing URL Dataset](https://www.kaggle.com/datasets/ndarvind/phiusiil-phishing-url-dataset)
and put the CSV in `data/raw/`.

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
