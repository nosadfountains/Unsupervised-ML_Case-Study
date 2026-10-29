# Mental Health in Technology-related Jobs: Clustering the OSMI Survey 2016

Code for my case study in the IU course DLBDSMLUSL01 – Machine Learning – Unsupervised Learning and Feature Engineering (Task 1). The analysis groups the respondents of the OSMI Mental Health in Tech Survey 2016 into segments, shows the segments in two reduced views (PCA and non-metric MDS), and describes each segment with the original answers, so that HR can derive points of leverage for a preemptive mental health program.

## Repository contents

| Path | Content |
|---|---|
| `mental-health-clustering/analysis.ipynb` | Complete analysis: exploration, preprocessing and feature engineering, PCA and MDS, k-means in two iterations, comparison with a Gaussian mixture model and Ward's clustering, stability checks, segment profiles |
| `mental-health-clustering/decisions.md` | All decisions and simplifications with their reasons and likely effects (basis of the critical reflection in the report) |
| `mental-health-clustering/data/column_map.csv` | Codebook: short name, original question, scale level, content group, who was asked, and the keep/drop decision per question |
| `mental-health-clustering/data/raw/` | Unchanged survey data (see "Data and license") |
| `mental-health-clustering/figures/` | Figures of the report |
| `mental-health-clustering/requirements.txt` | Package versions behind the results |

## How to run the notebook

1. Install Python 3.14 (the results were produced with Python 3.14.0 on macOS).
2. Open a terminal in the `mental-health-clustering` folder and install the packages:
   `python3.14 -m pip install -r requirements.txt`
   (on Windows: `py -3.14 -m pip install -r requirements.txt`)
3. Open `analysis.ipynb` in VS Code (with the Jupyter extension) or in Jupyter, select the Python 3.14 kernel, and choose **Run All**. If the editor asks for `ipykernel`, install it. A full run takes a few minutes; the two MDS cells take the longest.

The data path in the notebook is relative (`data/raw/...`), so the notebook has to be run from the `mental-health-clustering` folder. With other package versions, k-means may number the clusters differently; the notebook explains how to check this (segment sizes A 259, B 321, C 285, D 281).

`figures/clusters_2d.png` is saved by the notebook. The other figures (`scree_iteration1.png`, `scree_iteration2.png`, `k_selection_iteration2.png`, `ward_dendrogram_iteration2.png`) are the plots of the stored notebook run, exported unchanged for the appendix of the report.

## Results in brief

- Iteration 1 with all 1,433 respondents was shaped mainly by the survey's structure (self-employed vs. employed) and by health status. Iteration 2 therefore uses the 1,146 employees only.
- Final model: k-means with k = 4 on six principal components. All silhouette values stay below 0.25, so the segments divide a continuous range of answers rather than separate types; the four profiles replicate under a Gaussian mixture model and Ward's clustering.
- Segments: A "Comfortable and confident" (259), B "Open and supported" (321), C "Wary, with little support" (285), D "Cautious and not yet reached" (281).
- Results are reported only in aggregate and for groups of at least 10 people.

## Data and license

OSMI Mental Health in Tech Survey 2016 by Open Sourcing Mental Illness (OSMI), published on Kaggle: https://www.kaggle.com/datasets/osmi/mental-health-in-tech-2016

License: CC BY-SA 4.0 (https://creativecommons.org/licenses/by-sa/4.0/). The CSV in `mental-health-clustering/data/raw/` is the unchanged original file under its original name. The codebook `mental-health-clustering/data/column_map.csv` quotes the survey questions and is shared under the same license.

The case study task itself is not included, as IU International University of Applied Sciences holds its copyright.
