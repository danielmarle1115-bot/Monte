import numpy as np
import random

# -----------------------
# DATA
# -----------------------

target = np.array([
# paste 25 coordinates
])

populations = {
"Population1": np.array([ ]),
"Population2": np.array([ ]),
"Population3": np.array([ ]),
"Population4": np.array([ ]),
"Population5": np.array([ ])
}

# -----------------------
# SETTINGS
# -----------------------

iterations = 200000
pops_per_model = 4

# -----------------------
# PREP
# -----------------------

names = list(populations.keys())
vectors = list(populations.values())

best_distance = float("inf")
best_weights = None
best_indices = None

# -----------------------
# MAIN LOOP
# -----------------------

for _ in range(iterations):

    idx = random.sample(range(len(vectors)), pops_per_model)

    weights = np.random.random(pops_per_model)
    weights /= weights.sum()

    model = np.zeros(len(target))

    for i in range(pops_per_model):
        model += weights[i] * vectors[idx[i]]

    distance = np.linalg.norm(target - model)

    if distance < best_distance:
        best_distance = distance
        best_weights = weights
        best_indices = idx

# -----------------------
# RESULT
# -----------------------

print("\nBest Fit\n")

for i in range(pops_per_model):
    name = names[best_indices[i]]
    percent = best_weights[i] * 100
    print(name, round(percent,2), "%")

print("\nDistance:", best_distance)
