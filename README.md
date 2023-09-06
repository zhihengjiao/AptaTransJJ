# AptaTrans Pipeline

AptaTrans pipeline is a robust framework designed for the prediction and recommendation of Aptamer-Protein Interactions (API).

## Overview
- **Pre-training**: The pipeline pretrained AptaTrans's two encoders - one for aptamer ($encoder_{apta}$) and another for protein ($encoder_{prot}$). Pretraining tasks involve predicting masked tokens and secondary structures.
  
- **Fine-tuning**: After pretraining, AptaTrans is then fine-tuned for better performance.

- **API Prediction**: Use AptaTrans to predict the Aptamer-Protein Interaction scores.

- **Apta-MCTS Recommendation**: For any given target protein sequence, the pipeline can recommend candidate aptamer sequences via Apta-MCTS.

## Requirements
- pickle5
- torch
- tqdm
- numpy
- scikit-learn
- pandas
- sqlite3

## Getting Started

Here's a step-by-step guide on how to run the AptaTransPipeline:

Before you dive into the AptaTrans Pipeline, make sure to fetch the essential database files from our Google Drive:
- **bpRNA.db**
[![Download bpRNA.db](https://img.shields.io/badge/Download-protein_ss_keywords.db-blue?style=for-the-badge&logo=google-drive)](https://drive.google.com/file/d/178f_Tb3kgK4sZ23QAhqiK5YOUtkxjyMl/view?usp=sharing](https://drive.google.com/file/d/178f_Tb3kgK4sZ23QAhqiK5YOUtkxjyMl/view?usp=drive_link))
- **protein_ss_keywords.db**
[![Download protein_ss_keywords.db](https://img.shields.io/badge/Download-protein_ss_keywords.db-blue?style=for-the-badge&logo=google-drive)]([https://drive.google.com/file/d/1E1TIVtppA1L6nu0f8pYQKBAz2ZyBSCMY/view?usp=sharing](https://drive.google.com/file/d/1E1TIVtppA1L6nu0f8pYQKBAz2ZyBSCMY/view?usp=drive_link))

### 1. Initialize the Pipeline

```python
pipeline = AptaTransPipeline(
    d_model=128,
    d_ff=512,
    n_layers=6,
    n_heads=8,
    dropout=0.1,
    load_best_pt=True,
    device='cuda',
    seed=1004,
)
```

2. Pretrain the Aptamer Encoder
- Using the bpRNA dataset (accessible from the provided Google Drive link)
```python
pipeline.set_data_rna_pt(batch_size=68) # dataset from bpRNA
pipeline.pretrain_aptamer(epochs=1000, lr=1e-5)
```

3. Pretrain the Protein Encoder
- Using the PDB dataset (accessible from the provided Google Drive link)
```python
pipeline.set_data_protein_pt(batch_size=68) # dataset from PDB
pipeline.pretrain_protein(epochs=1000, lr=1e-5)
```

4. Fine-tune AptaTrans for API Prediction
```python
pipeline.set_data_for_training(batch_size=16)
pipeline.train(epochs=200, lr=1e-5)
```

5. Predict API Scores
- For given aptamer and target protein sequences
```python
# aptamer sequence
aptamer = 'AACGCCGCGCGUUUAACUUCC'
# target protein sequence
target = 'STEYKLVVVGADGVGKSALTIQLIQNHFVDEYDPTIEDSYRKQVVIDGETCLLDILDTAGQEEYSAMRDQYMRTGEGFLCVFAINNTKSFEDIHHYREQIKRVKDSEDVPMVLVGNKCDLPSRTVDTKQAQDLARSYGIPFIETSAKTRQGVDDAFYTLV
# get the score of API between aptamer sequence and protein sequence
pipeline.inference(aptamer, target)
```

6. Recommend Candidate Aptamers
- Using Apta-MCTS for a given target protein sequence:
```python
# Target protein sequence
target = 'STEYKLVVVGADGVGKSALTIQLIQNHFVDEYDPTIEDSYRKQVVIDGETCLLDILDTAGQEEYSAMRDQYMRTGEGFLCVFAINNTKSFEDIHHYREQIKRVKDSEDVPMVLVGNKCDLPSRTVDTKQAQDLARSYGIPFIETSAKTRQGVDDAFYTLVREIRKHKEKMSK'
# Recommend with AptaTransPipeline (consists of Apta-MCTS and AptaTrans)
pipeline.recommend(target, n_aptamers=5, depth=40, iteration=1000)
```
