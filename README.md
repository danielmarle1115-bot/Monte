let referencePopulations = [];

/* ===============================
   LOAD REFERENCES
=================================*/
async function loadReferences() {
    const response = await fetch("data/g25_references_scaled.txt");
    const text = await response.text();
    const lines = text.trim().split("\n");

    referencePopulations = lines.map(line => {
        const parts = line.split(",");
        return {
            name: parts[0],
            coords: parts.slice(1).map(Number),
            cluster: detectCluster(parts[0])
        };
    });

    console.log("Loaded:", referencePopulations.length);
}

/* ===============================
   CLUSTER DETECTION
=================================*/
function detectCluster(name) {
    const lower = name.toLowerCase();

    if (lower.includes("rusyn") || lower.includes("ukrain") || lower.includes("polish") || lower.includes("slovak"))
        return "Slavic";

    if (lower.includes("lithuan") || lower.includes("latv"))
        return "Baltic";

    if (lower.includes("serb") || lower.includes("croat") || lower.includes("bulgar") || lower.includes("romanian"))
        return "Balkan";

    if (lower.includes("german") || lower.includes("austrian"))
        return "Germanic";

    if (lower.includes("finn") || lower.includes("saami"))
        return "Uralic";

    return "Other";
}

/* ===============================
   EUCLIDEAN DISTANCE
=================================*/
function euclideanDistance(a, b) {
    let sum = 0;
    for (let i = 0; i < 25; i++) {
        sum += (a[i] - b[i]) ** 2;
    }
    return Math.sqrt(sum);
}

/* ===============================
   SINGLE DISTANCE RANKING
=================================*/
function rankClosest(target) {
    const results = referencePopulations.map(ref => ({
        name: ref.name,
        cluster: ref.cluster,
        distance: euclideanDistance(target, ref.coords)
    }));

    results.sort((a, b) => a.distance - b.distance);
    return results.slice(0, 25);
}

/* ===============================
   2-WAY MIXTURE MODEL
=================================*/
function twoWayModel(target) {
    let bestFit = { distance: Infinity };

    for (let i = 0; i < referencePopulations.length; i++) {
        for (let j = i + 1; j < referencePopulations.length; j++) {

            for (let p = 0; p <= 1; p += 0.02) {

                const mix = referencePopulations[i].coords.map((val, idx) =>
                    val * p + referencePopulations[j].coords[idx] * (1 - p)
                );

                const dist = euclideanDistance(target, mix);

                if (dist < bestFit.distance) {
                    bestFit = {
                        pop1: referencePopulations[i].name,
                        pop2: referencePopulations[j].name,
                        percent1: Math.round(p * 100),
                        percent2: Math.round((1 - p) * 100),
                        distance: dist
                    };
                }
            }
        }
    }

    return bestFit;
}

/* ===============================
   3-WAY MIXTURE MODEL
=================================*/
function threeWayModel(target) {
    let bestFit = { distance: Infinity };

    for (let i = 0; i < referencePopulations.length; i++) {
        for (let j = i + 1; j < referencePopulations.length; j++) {
            for (let k = j + 1; k < referencePopulations.length; k++) {

                for (let p = 0; p <= 1; p += 0.1) {
                    for (let q = 0; q <= 1 - p; q += 0.1) {

                        const r = 1 - p - q;

                        const mix = referencePopulations[i].coords.map((val, idx) =>
                            val * p +
                            referencePopulations[j].coords[idx] * q +
                            referencePopulations[k].coords[idx] * r
                        );

                        const dist = euclideanDistance(target, mix);

                        if (dist < bestFit.distance) {
                            bestFit = {
                                pop1: referencePopulations[i].name,
                                pop2: referencePopulations[j].name,
                                pop3: referencePopulations[k].name,
                                p1: Math.round(p * 100),
                                p2: Math.round(q * 100),
                                p3: Math.round(r * 100),
                                distance: dist
                            };
                        }
                    }
                }
            }
        }
    }

    return bestFit;
}

/* ===============================
   CLUSTER SUMMARY
=================================*/
function clusterBreakdown(rankedResults) {
    const clusters = {};

    rankedResults.forEach(r => {
        if (!clusters[r.cluster]) clusters[r.cluster] = 0;
        clusters[r.cluster]++;
    });

    return clusters;
}

/* ===============================
   INITIALIZE
=================================*/
async function initialize() {
    await loadReferences();
}

initialize();
