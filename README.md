# QC Pathfinder

A high-performance 3D interactive campus map and walking schedule planner for Queens College (CUNY). QC Pathfinder parses OpenStreetMap geospatial walkway data into a connected graph network, providing turn-by-turn routing, transit gap analysis between classes, and volumetric 3D building visualization.

---

## Key Features

### 1. 3D Isometric Campus Visualization
- **Volumetric Building Extrusions**: Custom-tuned 3D height profiles and structural tiers for Queens College landmarks (e.g., Kiely Hall's 48-meter clock tower, Rosenthal Library's atrium, Science Building, Student Union, and Fitzgerald Gym) using MapLibre GL `fill-extrusion` with directional sunlight shading.
- **Dynamic Camera Controls**: Perspective switching between top-down 2D orthographic and 52° axonometric 3D view with compass bearing orientation, angle presets, and free orbit tilt.
- **Priority-Tiered Roof Labels**: Collision-avoidant HTML label markers calculated with dynamic vertical roof offsets to float above buildings at varying zoom levels.

### 2. Topological Graph Routing Engine
- **OpenStreetMap Footpath Ingestion**: Ingests real-world walkway geometries, outdoor steps, and campus pathways into a topological graph representation with bidirectional edges.
- **Accessibility & Custom Routing Profiles**: Supports wheelchair-accessible flat routing (avoiding outdoor stairs) and adjustable pedestrian walking speeds (1.0 m/s slow, 1.3 m/s normal, 1.6 m/s brisk).
- **Node Snapping**: Connects arbitrary coordinates and building centroids to the nearest graph node via haversine snapping algorithms.

### 3. Student Day Planner & Schedule Transit Analysis
- **Class Timeline & Consecutive Legs**: Organizes weekly course schedules by day and computes the required walking time and distance between consecutive classes.
- **Slack & Gap Calculation**: Flags impossible transfers, tight transitions (< buffer time), and comfortable walking intervals based on real class start/end times.
- **Direct Navigation**: Real-time turn-by-turn path guidance showing remaining distance, live ETA, and campus geofence validation.

### 4. Interactive 3D Stop Manipulation & Custom Spots
- **3D Drag-and-Drop Relocation**: Move schedule stops directly across the 3D map. Supports real-time spatial feature queries (`queryRenderedFeatures`) across extruded building envelopes with golden highlight previews.
- **Repositioning Mode**: Click-to-move workflow allowing students to assign stops to specific buildings or open campus locations (quads, athletic fields, lawns).
- **Custom Campus Pins**: Create and save custom study spots, food locations, and outdoor meetups with custom categories and emojis.

---

## Technical Architecture

```
qc-pathfinder/
├── data/
│   ├── overrides.json        # Building aliases, centroids, and custom metadata
│   └── campus.geojson        # Generated GeoJSON containing building footprints & paths
├── scripts/
│   └── build-data.ts         # Pipeline script converting OSM data to GeoJSON & graph topology
├── src/
│   ├── components/
│   │   ├── CampusMap.tsx     # Core MapLibre GL 3D vector map, extrusion layers, and markers
│   │   ├── DayPlanner.tsx    # Class schedule timeline, transfer slack, and leg analysis
│   │   ├── DirectionsPanel.tsx # Point-to-point routing interface
│   │   ├── PlacesPanel.tsx   # Searchable campus directory & landmark index
│   │   └── AddStopModal.tsx  # Stop/class creation & detail configuration
│   ├── lib/
│   │   ├── routing.ts        # Dijkstra / A* shortest path search over campus graph
│   │   ├── geo.ts            # Haversine distance, node snapping, and bounding box checks
│   │   ├── time.ts           # Schedule leg computation, gap analysis, and time formatting
│   │   ├── navigation.ts     # GPS heading, geofencing, and accuracy circles
│   │   └── store.ts          # Zustand centralized state store with local persistence
│   ├── App.tsx               # Main application layout and modal orchestrator
│   └── main.tsx              # Application entry point
```

---

## Tech Stack

- **Frontend Framework**: React 19, TypeScript
- **Mapping & Geospatial**: MapLibre GL JS, OpenStreetMap vector data, GeoJSON
- **State Management**: Zustand (with persistent localStorage storage)
- **Styling**: Tailwind CSS
- **Icons**: Lucide React
- **Build Tool**: Vite
- **Testing**: Vitest

---

## Getting Started

### Prerequisites
- Node.js (v18 or higher recommended)
- npm or pnpm / yarn

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/qc-pathfinder.git
   cd qc-pathfinder
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. (Optional) Rebuild campus geospatial graph data:
   ```bash
   npm run build-data
   ```

4. Start the development server:
   ```bash
   npm run dev
   ```
   Open your browser to `http://localhost:3000`.

### Production Build

```bash
npm run build
```
Build output will be bundled to the `dist/` directory.

### Running Tests

```bash
npm test
```

---

## Routing Algorithm Details

The routing engine operates on an adjacency-list graph representation built from Queens College's physical path network:
1. **Edge Weight Calculation**: Edge weights represent physical meters calculated using the Haversine formula, adjusted by terrain factors (e.g. stairs cost multiplier when accessibility mode is disabled; complete avoidance when accessibility mode is enabled).
2. **A\* / Dijkstra Search**: Evaluates the shortest pedestrian path from origin to destination node.
3. **Path Interpolation**: Returns exact coordinate paths along physical footpaths rather than crude Euclidean straight lines.

---

## License

MIT License. Designed and developed for the Queens College student community.
