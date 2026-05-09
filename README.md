# Traffic Simulator - EDA (Road Network Preparation)

## Objective
This section prepares and validates the road-network data for the Traffic Simulator project using **OSMnx** and **NetworkX**.

Main goals:
- Build a drivable graph for Eindhoven.
- Add edge-level speed and travel-time attributes.
- Explore data quality and distributions.
- Compare distance-based vs time-based routing.
- Save a processed graph for reproducible next steps.

---

## Tools and Libraries
- Python
- `osmnx`
- `networkx`
- `pandas`
- `numpy`
- `matplotlib`
- `seaborn`

---

## Data Source
Road network data is downloaded from **OpenStreetMap** through OSMnx/Overpass API.

Types of data used:
- In-memory graph objects (`graph`, `graph_small`, `G`)
- Edge/node tables (`edges_gdf`, `nodes_gdf`)
- Local cache files (`cache/*.json`) from Overpass
- Saved processed graph (`data/eindhoven_drive_processed.graphml`)

---

## Workflow

### 1. Build the road graph
- Full area: `Eindhoven, Netherlands`
- Network type: `drive` (car network)

Optional subset for faster iteration:
- Center point near city center
- Radius-based graph (`dist=3000` meters)

### 2. Convert graph to EDA tables
Use:
- `ox.graph_to_gdfs(...)` for nodes and edges

This allows easy inspection of:
- `length`
- `highway`
- `maxspeed`
- `oneway`
- `geometry`

### 3. Add traffic-relevant edge attributes
Applied:
- `ox.add_edge_speeds(graph)` -> adds `speed_kph`
- `ox.add_edge_travel_times(graph)` -> adds `travel_time` (seconds)

### 4. EDA checks performed
- Missing-value checks on `length`, `speed_kph`, `travel_time`
- Summary statistics for speed and travel time
- Histograms:
  - `speed_kph` distribution
  - `travel_time` distribution
- Highway frequency (`value_counts`)
- Speed summary per road class (`groupby highway` with count/median/mean)

### 5. Route behavior baseline
For one origin-destination pair:
- Shortest route by `length`
- Shortest route by `travel_time`
- Compare whether routes are identical
- Compute total route distance and time for each
- Plot both routes on the same map

Result observed:
- Routes were different (`Same route? False`), confirming that cost function choice affects path selection.

---

## Key Interpretation
- The network is urban-dominant (`residential`, `tertiary`, etc.).
- Speed values cluster around common city limits (e.g., 30/50/70 km/h).
- Edge `travel_time` values are per segment (seconds), so route time must be summed across many edges.
- Time-optimized routing can differ from distance-optimized routing, which supports future congestion simulation work.

---

## Output Artifact
Processed graph saved to:

`data/eindhoven_drive_processed.graphml`

This file will be reused in the simulation phase to avoid repeating preprocessing.

---

## Next Step
Start **Simulation v1**:
- Apply congestion factors to selected edges.
- Update `travel_time`.
- Recompute time-optimal route.
- Compare with baseline route metrics from this EDA stage.
