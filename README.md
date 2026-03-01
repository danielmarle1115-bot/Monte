    In a real scenario, this would use a 36x25 matrix of coefficients.
    """import streamlit as st
import pandas as pd
import numpy as np

st.set_page_config(page_title="DNA K36 to G25 Simulator", layout="wide")

# 1. Load the weight matrix from your repository
@st.cache_data
def load_matrix():
    # Replace with the path to your CSV file
    return pd.read_csv("k36_to_g25_weights.csv", index_index=0)

try:
    weights_df = load_matrix()
    k36_labels = weights_df.index.tolist()
    
    st.title("🧬 Raw DNA to Simulated G25")
    st.markdown("Convert your Eurogenes K36 percentages into G25 coordinates.")

    # 2. User Input
    col1, col2 = st.columns([1, 1])
    with col1:
        st.subheader("Enter K36 Percentages")
        user_values = {}
        for label in k36_labels:
            user_values[label] = st.number_input(f"{label} (%)", min_value=0.0, max_value=100.0, step=0.01)

    # 3. Calculation
    if st.button("Generate Coordinates"):
        input_vector = np.array([user_values[l] for l in k36_labels])
        # Perform matrix multiplication
        simulated_g25 = np.dot(input_vector, weights_df.values) / 100
        
        with col2:
            st.subheader("Your Simulated G25 Coordinates")
            g25_str = ",".join([f"{val:.6f}" for val in simulated_g25])
            st.code(f"Simulated_G25_scaled,{g25_str}", language="text")
            
            st.download_button("Download Coordinates", f"Simulated_G25_scaled,{g25_str}", "g25_coords.txt")

except FileNotFoundError:
    st.error("Matrix file 'k36_to_g25_weights.csv' not found. Please upload it to your repository.")

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
