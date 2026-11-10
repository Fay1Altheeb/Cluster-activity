# 🎬 Movie Clustering Before and After PCA

Exploring how movie genres and audience reception shape movie clusters, and how dimensionality reduction changes those groups.

This project applies **K-Means clustering** before and after **Principal Component Analysis (PCA)**, comparing cluster separation, consistency, and interpretation.

## Research Question

How do genre combinations and audience reception relate to movie clusters, and how much does two-component PCA change the clustering results?

## Dataset

**Source:** [TMDB Top 10,000 Movies Dataset (2026)](https://www.kaggle.com/datasets/dhritisisodia/tmdb-top-10000-movies-dataset-2026)

- **Original dataset:** 10,000 movies
- **Duplicate movie IDs removed:** 36
- **Additional incomplete records removed:** 3
- **Final dataset:** 9,961 movies
- **Final features:** 22

## Features Used

| Feature | Description |
| --- | --- |
| Average rating | Audience evaluation of each movie |
| Log vote count | Number of votes, transformed to reduce skew |
| Log popularity | TMDB popularity score, transformed to reduce skew |
| Release year | Year extracted from the release date |
| 18 genre indicators | Binary columns representing each movie’s genres |

Movies can belong to multiple genres. Titles are used to interpret the results, not as clustering inputs.

## Project Workflow

### 1. Data Cleaning

- Remove duplicate movie IDs.
- Handle missing values in required columns.
- Filter invalid audience records.
- Convert release dates into numerical years.

### 2. Feature Preparation

- Convert genre IDs into readable names.
- Apply multi-hot encoding to represent multiple genres.
- Apply `np.log1p()` to vote counts and popularity.
- Standardize numerical and genre features separately.

### 3. Feature-Group Balancing

The dataset contains four numerical features and 18 genre indicators. Without balancing, the genre group would receive more total weight simply because it contains more columns.

After standardization:

- Numerical features are divided by `√4`.
- Genre features are divided by `√18`.

This balances the groups’ total variance. It does not make their contribution equal for every pair of movies.

### 4. K-Means Before PCA

- Evaluate K values from 2 to 8.
- Examine inertia using the elbow method.
- Compare silhouette scores.
- Select **K = 2** for broad groupings and **K = 7** for more detailed profiles.

### 5. PCA and Re-Clustering

- Reduce the 22 features to two principal components.
- Fit K-Means again using K = 2 and K = 7.
- Compare separation and cluster assignments before and after PCA.

## Results

### Explained Variance

| Component | Explained Variance |
| --- | ---: |
| PC1 | 23.49% |
| PC2 | 15.73% |
| **Combined** | **39.23%** |

The combined percentage is calculated from unrounded values.

PCA preserves all 9,961 movies but retains only **39.23% of the variance** in the prepared feature space.

### Clustering Comparison

| K | Silhouette Before PCA | Silhouette After PCA | ARI |
| --- | ---: | ---: | ---: |
| 2 | 0.172 | 0.405 | **0.970** |
| 7 | 0.111 | 0.345 | **0.338** |

**Silhouette Score** measures how compact and separated clusters are in the space being evaluated.

**Adjusted Rand Index (ARI)** measures agreement between the before-PCA and after-PCA cluster assignments, adjusted for chance. It is not accuracy or the percentage of movies that kept their cluster.

These results are taken from the notebook’s saved outputs.

## Key Findings

### K = 2: Broad Audience Groups

The two clusters mainly separate movies by audience engagement:

- **Lower engagement:** Approximately 774 votes per movie, an average rating of 6.60, and popularity of 2.39.
- **Higher engagement:** Approximately 5,050 votes per movie, an average rating of 7.03, and popularity of 8.51.

Examples in the higher-engagement cluster include *Interstellar*, *Inception*, and *The Avengers*.

The ARI of **0.970** indicates that these broad groupings remain highly consistent after PCA.

### K = 7: More Detailed Movie Profiles

The seven clusters reveal more specific combinations of ratings, engagement, and genres:

| Cluster | Profile |
| --- | --- |
| 0 | Highly popular movies |
| 1 | Lower-rated, low-engagement movies |
| 2 | Well-rated movies with moderate engagement |
| 3 | Popular, moderately rated movies |
| 4 | Lower-rated movies with moderate engagement |
| 5 | Highly rated, less popular movies |
| 6 | Moderately rated, low-engagement movies |

These interpretations describe the **after-PCA clusters**.

The ARI of **0.338** indicates that the detailed groupings change substantially after dimensionality reduction.

## Why Higher Silhouette Scores Need Careful Interpretation

Silhouette scores increase after PCA, but the scores are calculated in different feature spaces:

- Before PCA: 22 balanced features
- After PCA: two principal components

A cleaner two-dimensional separation does not necessarily mean better clustering of the original data. PCA leaves out substantial variation, which can change the distances and relationships between movies.

## Conclusion

Movie groups reflect a combination of genres, ratings, popularity, and audience engagement rather than genre alone.

Two-component PCA preserves the broad K = 2 structure much better than the detailed K = 7 structure. It helps visualize the data, but it can remove information that matters for detailed movie profiles.

**A clearer plot does not necessarily mean a more faithful representation of the original data.**

## Tools and Libraries

- Python
- pandas
- NumPy
- Matplotlib
- scikit-learn
- Jupyter Notebook / Google Colab

## How to Run

1. Open `Movies_Clustring.ipynb` in Jupyter Notebook or Google Colab.
2. Download the dataset from the Kaggle link above.
3. Place `TMDB_Top_10000_Movies_2026.csv` in the notebook’s working directory, or update the path in the loading cell.
4. Install the required packages if needed:

   `pip install numpy pandas matplotlib scikit-learn jupyter`

5. Run the notebook cells from top to bottom.

For Google Colab, upload the CSV through the Files panel before running the notebook.

## Reproducibility

- K-Means: `random_state=42`, `n_init=10`
- Silhouette evaluation: `sample_size=2000`, `random_state=42`
- PCA: `n_components=2`

Results may vary with dataset changes or library versions.

