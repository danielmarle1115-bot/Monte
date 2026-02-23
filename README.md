<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>G25 Monte Carlo Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            max-width: 900px;
        }
        h1, h2, h3 {
            color: #2c3e50;
        }
        textarea {
            width: 100%;
            font-family: monospace;
            font-size: 14px;
            margin-bottom: 10px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-bottom: 20px;
        }
        button:hover {
            background-color: #2980b9;
        }
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
    <p>Paste your 25 G25 values, separated by commas:</p>
    <textarea id="g25Input" rows="3" placeholder="0.0123,0.0234,..."></textarea>
    <br>
    <button onclick="runAnalysis()">Run Monte Carlo</button>

    <div id="singleResult"></div>
    <div id="twoWayResult"></div>
    <div id="threeWayResult"></div>

    <script>
        // Load your reference populations from the data folder
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

        // Monte Carlo Functions (your existing ones)
        // Placeholder functions — replace with your full Monte Carlo functions
        function rankClosest(userCoords) {
            // Returns top 10 closest single populations
            return populations
                .map(p => {
                    let dist = 0;
                    for (let i = 0; i < 25; i++) dist += Math.pow(p.coords[i] - userCoords[i], 2);
                    return { name: p.name, distance: Math.sqrt(dist) };
                })
                .sort((a,b) => a.distance - b.distance);
        }

        function twoWayModel(userCoords) {
            // Example: compute best 2-way mix (simplified)
            // Replace with your full algorithm
            return populations.slice(0,5).map(p => ({
                mix: [p.name, populations[(populations.indexOf(p)+1)%populations.length].name],
                score: Math.random().toFixed(4)
            }));
        }

        function threeWayModel(userCoords) {
            // Example: compute best 3-way mix (simplified)
            return populations.slice(0,5).map(p => ({
                mix: [p.name, populations[(populations.indexOf(p)+1)%populations.length].name,
                      populations[(populations.indexOf(p)+2)%populations.length].name],
                score: Math.random().toFixed(4)
            }));
        }

        // Function to run analysis
        function runAnalysis() {
            const input = document.getElementById("g25Input").value.trim();
            const coords = input.split(",").map(Number);

            if (coords.length !== 25 || coords.some(isNaN)) {
                alert("Please enter exactly 25 valid numbers, separated by commas.");
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
