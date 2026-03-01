    
    # Ensure populations matcimport streamlit as st
import pandas as pd
import numpy as np

# Title and Description
st.title("🧬 Raw DNA to K36 to G25 Simulator")
st.markdown("""
This app simulates G25 coordinates from Eurogenes K36 results using linear regression.
Paste your Eurogenes K36 results (components and percentages) below.
""")

# 1. Input K36 Results
st.subheader("1. Paste Eurogenes K36 Results")
k36_input = st.text_area("Enter K36 Admixture Proportions (e.g., Amerindian 5.0, Arab 10.0...)", height=200)

# 2. Simulation Logic (Mock Implementation)
# A real implementation requires a pre-trained linear regression model (matrix)
# that maps 36 k36 components to 25 G25 dimensions.
def simulate_g25(k36_data):
    """
    Simulates G25 using a mock linear regression approach.
    In a real scenario, this would use a 36x25 matrix of coefficients.
    """
    # Parse K36 data
    try:
        lines = k36_data.strip().split('\n')
        k36_dict = {}
        for line in lines:
            parts = line.replace('''').split()
            if len(parts) >= 2:
                k36_dict[parts[0]] = float(parts[1])
        
        # Ensure we have all 36 components (simplified here)
        # Real formula: G25 = K36_Vector * Regression_Matrix
        # This is a placeholder for the mathematical transformation
        simulated_coords = np.random.normal(0, 0.05, 25) # MOCK DATA
        return simulated_coords
    except Exception as e:
        return None

# 3. Process and Output
if st.button("Generate Simulated G25"):
    if k36_input:
        with st.spinner("Calculating..."):
            g25_results = simulate_g25(k36_input)
            
            if g25_results is not None:
                st.success("Simulation Complete!")
                
                # Format G25 output
                name = "Sample_K36_Sim"
                formatted_coords = "".join([f"{val:.6f}" for val in g25_results])
                final_output = f"{name}{formatted_coords}"
                
                st.subheader("Simulated G25 Scaled Coordinates")
                st.code(final_output)
                st.text("Copy this string for Vahaduo or other calculators.")
            else:
                st.error("Error parsing K36 data. Please check format.")
    else:
        st.warning("Please enter K36 data.")

# Disclaimer
st.markdown("---")
st.caption("Disclaimer: These are simulated coordinates and do not replace real G25 coordinates from Davidski.")
h between results and matrix
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
