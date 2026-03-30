# 🧬 COVID-19 Biomedical Knowledge Graph

A Python-based project that constructs an interactive biomedical knowledge graph from entity-relationship data extracted from scientific literature, combined with a Naive Bayes classifier for gene-to-disease prediction.

---

## 📌 Overview

This project processes a biomedical dataset of entity pairs (Genes, Diseases, Chemicals) extracted from COVID-19-related research papers. It builds an interactive, force-directed knowledge graph using **pyecharts** and trains a **Multinomial Naive Bayes** classifier to predict disease associations from gene inputs.

---

## 🗂️ Project Structure

```
Covid_Knowledge_graph/
│
├── Covid_Knowledge_graph.ipynb   # Main Jupyter Notebook
├── marked_sentence.csv           # Input dataset (not included — see Data section)
├── enhanced_knowledge_graph_1000.html  # Generated graph output
├── prediction_and_graph_result.html    # Combined prediction + graph output
└── README.md
```

---

## 📊 Dataset

The input file `marked_sentence.csv` contains biomedical entity pairs extracted from scientific text. Each row represents a relationship between two entities.

| Column | Description |
|---|---|
| `start_entity` | Source entity name (e.g., `CD8A`, `COVID-19`, `Aspirin`) |
| `end_entity` | Target entity name |
| `start_entity_type` | Type of source entity: `Gene`, `Disease`, or `Chemical` |
| `end_entity_type` | Type of target entity: `Gene`, `Disease`, or `Chemical` |
| `marked_sentence` | Supporting sentence from literature with `start_entity` / `end_entity` placeholders |

> **Note:** The dataset is not included in this repository. Place your `marked_sentence.csv` in the project root directory before running the notebook.

Relation types are inspired by the Percha & Altman (2018) biomedical relationship taxonomy:
- `chemical-disease`: T, C, Sa, Pr, Pa, J
- `gene-disease`: U, Ud, D, J, Te, Y, G
- `gene-gene`: B, W, V+, E+, E, I, H, Rg, Q
- and more (see `Conf` class in the notebook)

---

## ✨ Features

- **Interactive Knowledge Graph** — Force-directed graph rendered as HTML using pyecharts, with color-coded entity types:
  - 🟠 Disease (`#FF7F50`)
  - 🟢 Chemical (`#B3EE3A`)
  - 🩵 Gene (`#48D1CC`)
- **Node sizing** — Node size scales logarithmically with the entity's degree (number of connections)
- **Edge labels** — Optionally display relationship confidence scores on edges
- **Gene → Disease Prediction** — Multinomial Naive Bayes classifier trained on Gene–Disease pairs
- **Prediction visualization** — Correct/incorrect predictions are color-coded on graph edges (green/red)
- **HTML Export** — Full-page interactive graph exported to `.html` for sharing

---

## 🛠️ Installation

### Prerequisites

- Python 3.8+
- pip

### Install dependencies

```bash
pip install pandas numpy scikit-learn pyecharts torch transformers tqdm opencv-python selenium playwright
```

For Playwright (used for HTML-to-image rendering):

```bash
playwright install chromium
```

For Selenium (alternative renderer):
- Install [ChromeDriver](https://chromedriver.chromium.org/) and ensure it matches your Chrome version.

---

## 🚀 Usage

1. Clone the repository:

```bash
git clone https://github.com/your-username/covid-knowledge-graph.git
cd covid-knowledge-graph
```

2. Place your `marked_sentence.csv` in the project root.

3. Open and run the notebook:

```bash
jupyter notebook Covid_Knowledge_graph.ipynb
```

4. Update the CSV path in data-loading cells:

```python
# Change this to your actual file path
df = pd.read_csv("marked_sentence.csv")
```

5. After running, the outputs will be saved as:
   - `enhanced_knowledge_graph_1000.html` — interactive knowledge graph
   - `prediction_and_graph_result.html` — prediction result + embedded graph

---

## 🧠 ML Model

A **Multinomial Naive Bayes** classifier is trained on Gene–Disease entity pairs:

- **Features:** Label-encoded gene names
- **Target:** Label-encoded disease names
- **Split:** 70% train / 30% test
- **Inference:** Given a gene name, predict the most likely associated disease

```python
# Example inference
input_gene = "CAP"
predicted_disease = predict_disease(input_gene)
# Output: "COVID-19"
```

> ⚠️ The current model accuracy is approximately **19.8%** due to the high number of disease classes (549+) and sparse per-gene data. See the [Limitations](#️-limitations) section for improvement suggestions.

---

## 📉 Model Performance

| Metric | Value |
|---|---|
| Accuracy | ~19.8% |
| Macro Avg F1 | ~0.00 |
| Weighted Avg F1 | ~0.07 |
| Test Samples | 2,714 |

The model tends to predict the most frequent class (COVID-19) for most inputs due to class imbalance.

---

## ⚠️ Limitations

- The Naive Bayes classifier uses only a label-encoded gene ID as input, which is not semantically meaningful — it cannot generalize to unseen genes.
- Severe class imbalance (~20% of samples belong to one disease class) causes the model to default to that class.
- The `pred` (relationship confidence) column used for graph edge weights is not present in the base dataset and must be added externally (e.g., from a relation extraction model).
- Graph rendering at scale (1,000+ nodes) may be slow in the browser.

---

## 🔮 Future Improvements

- Replace Naive Bayes with a **transformer-based relation classifier** (e.g., fine-tuned BioBERT) using the `marked_sentence` context
- Add **relation type classification** using the Percha & Altman taxonomy
- Use **NetworkX** for graph analytics (centrality, community detection)
- Integrate **Neo4j** or **RDF store** for persistent graph storage
- Add **multi-hop reasoning** over the knowledge graph

---

## 📚 References

- Percha B, Altman R B. *A global network of biomedical relationships derived from text.* Bioinformatics, 2018, 34(15): 2614–2624.
- [pyecharts Documentation](https://pyecharts.org/)
- [scikit-learn MultinomialNB](https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.MultinomialNB.html)

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

## 🙋 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
