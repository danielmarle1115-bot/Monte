            })
            .catch(err => consimport pandas as pd
import numpy as np
import admix
import sys

# 1. SETUP: Load the Conversion Matrix
# This matrix maps 36 K36 populations to 25 G25 dimensions
def load_matrix(matrix_path):
    return pd.read_csv(matrix_path, index_col=0)

# 2. STEP ONE: Raw DNA to K36
def run_k36_analysis(dna_file):
    print("Extracting SNPs and calculating K36 Admixture...")
    # The 'eurogenes_k36' model must be in your /db folder
    model = admix.load_model("db/eurogenes_k36.json")
    results = model.predict(dna_file)
    return results # Returns a dictionary of 36 percentages

# 3. STEP TWO: K36 to G25 (The Simulation)
def project_to_g25(k36_scores, matrix):
    print("Projecting K36 results to Simulated G25 coordinates...")
    
    # Ensure populations match between results and matrix
    pops = matrix.index.tolist()
    score_vector = np.array([k36_scores.get(p, 0) for p in pops])
    
    # Matrix Multiplication: (1x36) * (36x25) = (1x25)
    g25_coords = np.dot(score_vector, matrix.values)
    return g25_coords

if __name__ == "__main__":
    raw_file = "AncestryDNA_DM.txt" # Your uploaded file
    
    # Load transformation weights
    vbn_matrix = load_matrix("matrix_k36_g25.csv")
    
    # Execute Pipeline
    k36_data = run_k36_analysis(raw_file)
    g25_result = project_to_g25(k36_data, vbn_matrix)
    
    # Format output for Vahaduo
    output = "User_Simulated_Scaled," + ",".join(map(str, np.round(g25_result, 6)))
    print("\n--- FINAL G25 COORDINATES ---")
    print(output)ole.error("Error loading reference populations:", err));

        // Placeholder Monte Carlo functions — replace with your full functions
        function rankClosest(userCoords) {
            return populations
                .map(p => {
                    let dist = 0;
                    for (let i = 0; i < 25; i++) dist += Math.pow(p.coords[i] - userCoords[i], 2);
                    return { name: p.name, distance: Math.sqrt(dist) };
                })
                .sort((a,b) => a.distance - b.distance);
        }

        function twoWayModel(userCoords) {
            return populations.slice(0,5).map(p => ({
                mix: [p.name, populations[(populations.indexOf(p)+1)%populations.length].name],
                score: Math.random().toFixed(4)
            }));
        }

        function threeWayModel(userCoords) {
            return populations.slice(0,5).map(p => ({
                mix: [p.name, populations[(populations.indexOf(p)+1)%populations.length].name,
                      populations[(populations.indexOf(p)+2)%populations.length].name],
                score: Math.random().toFixed(4)
            }));
        }

        // Flexible run function: accepts commas, spaces, or line breaks
        function runAnalysis() {
            const input = document.getElementById("g25Input").value.trim();
            const coords = input
                .split(/[\s,]+/)           // splits by comma, space, or line break
                .map(s => Number(s.trim())) // trims each
                .filter(n => !isNaN(n));   // removes any invalid values

            if (coords.length !== 25) {
                alert("Please enter exactly 25 numbers.");
                return;
            }

            const ranked = rankClosest(coords);
            const best2 = twoWayModel(coords);
            const best3 = threeWayModel(coords);

            document.getElementById("singleResult").innerHTML =
                "<h3>Closest Single Matches</h3><pre>" + JSON.stringify(ranked.slice(0,10), null, 2) + "</pre>";

            document.getElementById("twoWayResult").innerHTML =
                "<h3>Best 2-Way Mixtures</h3><pre>" + JSON.stringify(best2, null, 2) + "</pre>";

            document.getElementById("threeWayResult").innerHTML =
                "<h3>Best 3-Way Mixtures</h3><pre>" + JSON.stringify(best3, null, 2) + "</pre>";
        }
    </script>
</body>
</html>
