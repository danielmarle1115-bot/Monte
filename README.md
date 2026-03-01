            background-color: #2980b9;
        }import pandas as pd
import numpy as np

# Step 1: Parse Ancestry File
def parse_ancestry(file_path):
    df = pd.read_csv(file_path, sep='\t', comment='#')
    return df

# Step 2: Simplified Linear Regression for G25 (Pseudo-code)
def k36_to_g25(k36_results):
    # k36_results is a dict of { 'Population': Percentage }
    g25_coords = []
    # Coefficients is a pre-defined matrix for the 25 dimensions
    for dim in range(1, 26):
        coord = sum(k36_results[pop] * coefficients[dim][pop] for pop in populations)
        g25_coords.append(coord)
    return g25_coords
        pre {
            background-color: #f4f4f4;
            padding: 10px;
            border-radius: 5px;
            overflow-x: auto;
        }
    </style>
</head>
<body>
    <h1>G25 Monte Carlo Calculator</h1>

    <h2>Enter Your G25 Coordinates</h2>
    <p>Paste your 25 G25 values — commas, spaces, or line breaks are all fine:</p>
    <textarea id="g25Input" rows="5" placeholder="0.0123,0.0234,... or 0.0123 0.0234 ..."></textarea>
    <br>
    <button onclick="runAnalysis()">Run Monte Carlo</button>

    <div id="singleResult"></div>
    <div id="twoWayResult"></div>
    <div id="threeWayResult"></div>

    <script>
        // Load reference populations from the data folder
        let populations = [];

        fetch('data/g25_references_scaled.txt')
            .then(response => response.text())
            .then(text => {
                const lines = text.trim().split('\n');
                populations = lines.map(line => {
                    const [name, ...coords] = line.split(',');
                    return { name: name.trim(), coords: coords.map(Number) };
                });
                console.log("Reference populations loaded:", populations.length);
            })
            .catch(err => console.error("Error loading reference populations:", err));

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
