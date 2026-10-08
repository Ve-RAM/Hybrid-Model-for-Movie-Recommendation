# 🎬 Hybrid Movie Recommender System: A Non-Traditional Approach

A hybrid, multi-objective movie recommendation system that goes beyond
accuracy. Instead of only recommending titles similar to what a user already
likes, it simultaneously optimizes **accuracy**, **novelty** and **diversity**
using the **NSGA-II** genetic algorithm, and returns a Pareto front of
candidate recommendation lists.

> Project developed at **Universidad EAFIT** (School of Applied Sciences and
> Engineering). Based on the model proposed by Bavera, Baran & Yael,
> *"Sistema híbrido de recomendación. Un sistema multi-objetivo no
> convencional"* (CLEI 2019).

## 👥 Authors

- Jaime Andrés Jaramillo R.
- Juan Pablo Castaño M.
- Santiago Vera Ramírez

## 📖 Overview

Traditional recommenders tend to maximize a single metric (usually accuracy),
which often results in repetitive content that can bore the user. This project
frames recommendation as a **multi-objective optimization problem** with three
objectives to be maximized for each user:

| Objective | Description |
|-----------|-------------|
| **Accuracy** | How well the recommended titles match the user's taste (based on a user profile built from their rated movies). |
| **Novelty** | How unlikely it is that the user has already seen the movie (based on how many users have rated it). |
| **Diversity** | How different the recommended movies are from each other (weighted combination of Euclidean and cosine distances over genre/feature vectors). |

The problem is solved with **NSGA-II** (Non-dominated Sorting Genetic
Algorithm II), which yields a **Pareto front** of recommendation lists
representing different trade-offs between the three objectives.

## 🧮 Mathematical Formulation

**Decision variable:** `x_{u,p} ∈ {0,1}`, equal to 1 if movie `p` is
recommended to user `u`.

**Objectives**

- Accuracy: `Fe(u) = (1/k) · Σ r_{u,p} · x_{u,p}`
- Novelty: `Fn(u) = Σ (1 − P(seen | p)) · x_{u,p}`
- Diversity: `Fd(u) = α · Fd_eucl(u) + (1 − α) · Fd_cos(u)`

**Constraints**

1. Only available movies can be recommended.
2. Exactly `k` movies per recommendation list.
3. Minimum rating threshold (`η_min`).
4. Do not recommend movies the user has already rated.
5. Minimum number of distinct genres (`m`) in each list.
6. Maximum total popularity (`T_Pop`) per list.
7. Minimum novelty per movie (`T_Nov`).
8. Binary decision variables (no duplicates).

## ⚙️ Methodology

1. **Data collection**: MovieLens (GroupLens, University of Minnesota),
   *small* dataset: 100,836 ratings and 3,683 tags over 9,742 movies from
   610 users.
2. **Preprocessing**: movie × genre matrix, movie–genre dictionary and a
   normalized popularity matrix.
3. **Metric functions**: accuracy (based on a user profile), novelty
   (based on popularity) and diversity (cosine distance).
4. **Candidate selection**: movies unseen by the user whose novelty is above
   the threshold derived from the maximum desired popularity percentile.
5. **Evolutionary search**: custom `FitnessM` and `Individual` classes
   (DEAP-style) and NSGA-II with non-dominated sorting, crowding distance,
   binary tournament selection, crossover and mutation, stopping after a fixed
   number of generations.
6. **New users**: a function to register new users and their ratings so they
   can receive recommendations.

## 📊 Results

| Case | User | Accuracy | Novelty | Diversity |
|------|------|----------|---------|-----------|
| 1 | MovieLens user ID 1 (232 ratings) | 0.6194 | 0.9080 | 0.6590 |
| 2 | New user with 3 ratings | 0.4450 | 0.9771 | 0.8787 |

- With many ratings, the model gives more weight to accuracy.
- With few ratings, it prioritizes diversity and novelty (12 different
  genres in the recommended list for the second case).
- Recommendations are compared empirically against those of platforms like
  Netflix, showing the impact of including novelty and diversity.

### Sensitivity analysis

| Parameter | Observation |
|-----------|-------------|
| `MIN_GENRES` | Values 3–7 have no effect; from 8 onwards accuracy drops sharply while novelty and diversity approach their maximum. |
| `N_GEN` | 100–150 generations give a good balance; more generations over-optimize accuracy/novelty at the cost of diversity and compute time. |
| `POP_SIZE` | Larger populations find more balanced Pareto points (best composite score Z = 2.306 at 960) but are computationally expensive. |

## 🔧 Main Parameters

| Parameter | Description |
|-----------|-------------|
| `k` | Number of movies recommended per user |
| `MIN_GENRES` | Minimum number of distinct genres per list |
| `N_GEN` | Number of generations of NSGA-II |
| `POP_SIZE` | Population size |
| `alpha` | Weight between Euclidean and cosine distances |
| `T_Pop` / `T_Nov` | Popularity and novelty thresholds |
| `eta_min` | Minimum rating to consider an item relevant |

## 🛠️ Tech Stack

- Python
- DEAP (evolutionary algorithms)
- NumPy / Pandas
- Matplotlib (3D Pareto front visualization)

## ✅ Conclusions

- Multi-objective optimization enables recommendation lists that are not
  only accurate, but also novel and diverse.
- Stricter diversity constraints increase novelty and diversity at the cost
  of accuracy.
- Larger populations and more generations enlarge the set of feasible
  solutions, but with diminishing returns and higher computational cost.

## 📚 References

1. M. Bavera, B. Baran, U. Yael, "Sistema híbrido de recomendación. Un
   sistema multi-objetivo no convencional," CLEI 2019.
2. F. M. Harper and J. A. Konstan, "The MovieLens Datasets: History and
   Context," ACM TiiS, vol. 5, no. 4, 2015. DOI: 10.1145/2827872
3. F.-A. Fortin et al., "DEAP: Evolutionary Algorithms Made Easy," JMLR,
   vol. 13, 2012.
4. K. Deb and H. Jain, "An Evolutionary Many-Objective Optimization
   Algorithm Using Reference-Point-Based Nondominated Sorting Approach,"
   IEEE TEVC, vol. 18, no. 4, 2014.
5. pymoo, [NSGA-II documentation](https://pymoo.org/algorithms/moo/nsga2.html)
6. pagmo2, [NSGA-II documentation](https://esa.github.io/pagmo2/docs/cpp/algorithms/nsga2.html)

## 📄 License

This project is released under the MIT License (update as appropriate).
