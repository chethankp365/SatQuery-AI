<div align="center">

<img src="./assets/hero-banner.svg" alt="SatQuery AI" width="100%"/>

<br/>

![Smart India Hackathon 2026](https://img.shields.io/badge/Smart%20India%20Hackathon-2026-FF9933?style=flat-square)
![Problem Statement](https://img.shields.io/badge/Problem%20Statement-SIH26167-138808?style=flat-square)
![Theme](https://img.shields.io/badge/Theme-Space%20Technology-0b3d5c?style=flat-square)
![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![YOLOv8](https://img.shields.io/badge/YOLOv8-111F68?style=flat-square)
![Gemini](https://img.shields.io/badge/Gemini%202.5-8E75B2?style=flat-square&logo=googlegemini&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white)

**An interactive vision-language assistant for multimodal remote-sensing analysis through plain-text queries.**

[Demo video](https://drive.google.com/file/d/1uGtNYGqfHxEvuASyZqh0dhKWTsbKI3WQ/view?usp=drive_link) · [How it works](#how-it-works) · [Screenshots](#console-screenshots) · [Feasibility](#feasibility)

</div>


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Overview

Satellite imagery holds the answers to questions about floods, forest loss and port activity, but getting those answers still means **data → GIS tools → a trained analyst**. That takes time that disaster response and security teams do not have.

SatQuery AI replaces that chain with a single step:

> **question → AI agent → specialist models → multi-sensor evidence → actionable answer**

Ask a question in plain language. An agentic orchestrator validates the input, picks the right models (object detection, change detection, spectral indices, a vision-language model), fuses optical, SAR and temporal evidence, and returns an answer where every number is traceable to a model output.


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## See it in action

The loop below shows one full query: typed, routed through the tool pipeline, and answered with grounded detections on the map.

<div align="center">
  <img src="./assets/query-demo.svg" alt="Animated demo of a query being typed, routed and answered with ship detections" width="100%"/>
</div>


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## How it works

<div align="center">
  <img src="./assets/pipeline.svg" alt="Five-stage animated pipeline" width="100%"/>
</div>

<br/>

### Automatic model selection

Different questions light up different paths. A ship-counting query uses the detector and the VLM. A flood query switches to spectral indices, change detection and SAR.

<div align="center">
  <img src="./assets/agent-dag.svg" alt="Agent selects different specialist models per query" width="100%"/>
</div>

### Full flow

```mermaid
flowchart LR
    U([Analyst]) -->|plain-language query| A{{Agentic Orchestrator<br/>LLM tool-calling DAG}}
    A --> V[Input and metadata validation]
    V --> I[Intent detection]
    I --> T1[YOLOv8<br/>object detection]
    I --> T2[NDWI / NDVI / NBR<br/>spectral indices]
    I --> T3[SSIM / CVA<br/>change detection]
    I --> T4[Gemini 2.5 VLM<br/>scene reasoning]
    T1 & T2 & T3 & T4 --> F[Optical, SAR and temporal fusion]
    F --> E[Evidence and confidence score]
    E --> M[Geospatial visualization]
    M --> R([Answer · Alert · Dossier])
    E -. audit .-> DB[(MongoDB audit trail)]

    style A fill:#0b3d5c,stroke:#22d3ee,color:#fff
    style F fill:#0b3d5c,stroke:#22d3ee,color:#fff
    style R fill:#064e3b,stroke:#34d399,color:#fff
```

### Sequence of a single query

```mermaid
sequenceDiagram
    autonumber
    actor User as Commander
    participant UI as Console (React)
    participant API as REST API
    participant Agent as Orchestrator
    participant CV as CV models
    participant VLM as Gemini VLM
    participant DB as Audit DB

    User->>UI: "Count all cargo and naval ships in this harbor area"
    UI->>API: query + AOI bounding box + sensor composite
    API->>Agent: validate input, detect intent
    Agent->>CV: run YOLOv8 on the selected AOI
    CV-->>Agent: boxes, counts, GeoJSON
    Agent->>VLM: scene context
    VLM-->>Agent: qualitative description
    Note over Agent: Numbers come only from CV output (strict grounding)
    Agent-->>API: structured intelligence card + confidence
    API->>DB: log query, tools used, result
    API-->>UI: markers, metrics, citations
```


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Multi-sensor intelligence

### Optical + SAR fusion
When clouds block the optical pass, SAR fills the gap, so the answer never goes dark.

<div align="center">
  <img src="./assets/sensor-fusion.svg" alt="Optical and SAR fusion producing a cloud-free result" width="100%"/>
</div>

### Change detection
Bi-temporal SSIM / CVA and NDWI thresholds turn two passes into a change map with a measured area.

<div align="center">
  <img src="./assets/change-detection.svg" alt="Before and after swipe comparison with change map" width="100%"/>
</div>

### Proactive alerts
AOIs are monitored continuously, and each alert opens the affected mission in one click.

<div align="center">
  <img src="./assets/alerts.svg" alt="Active mission change alerts" width="100%"/>
</div>

<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>

## Key features

| | |
|:---|:---|
| **Natural-language tasking** | Ask by text or voice, with no GIS workflow required |
| **Agentic orchestration** | A tool-calling DAG routes each query to the right specialist model |
| **Multi-sensor fusion** | Optical, SAR and multi-temporal data in one view, with SAR fallback when clouds block optical |
| **Strict number grounding** | Counts and areas come only from CV models, never from the language model |
| **Change detection** | Bi-temporal SSIM / CVA heatmaps plus NDWI and NDVI deltas |
| **Proactive alerts** | Monitored AOIs raise flood, deforestation and traffic alerts automatically |
| **Access control and audit** | Gateway login, clearance roles and a MongoDB audit trail |
| **Exportable dossiers** | PDF, GeoJSON and CSV metrics in one click |


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Console screenshots

Screenshots from the running prototype.

**Ground station gateway.** Operator authentication before the console opens.

<div align="center">
  <img src="./Screenshot_2026-09-04_014815.png" alt="Ground station authentication" width="85%"/>
</div>

**Agentic query console.** A harbor query being routed over Mumbai Port & Naval Dockyard.

<div align="center">
  <img src="./Screenshot_2026-09-04_015506.png" alt="Query routing in the console" width="100%"/>
</div>

**Full tactical HUD.** Monitored zones, AOI bounding box, sensor composites, map toggles and the intelligence panel.

<div align="center">
  <img src="./Screenshot_2026-09-04_022044.png" alt="Full HUD mission console" width="100%"/>
</div>

**Proactive monitor alerts.** The system watches AOIs and surfaces changes with a one-click inspect action.

<div align="center">
  <img src="./Screenshot_2026-09-04_022529.png" alt="Proactive monitor alert" width="100%"/>
</div>

**Any mission, any query.** The same console handling a Western Ghats forest-fire query.

<div align="center">
  <img src="./Screenshot_2026-09-04_110451.png" alt="Forest fire query in Western Ghats" width="100%"/>
</div>

**Built-in architecture and alerts view.**

<div align="center">
  <img src="./Screenshot_2026-09-04_093543.png" alt="Architecture and alerts view" width="100%"/>
</div>

**Compact query panel.**

<div align="center">
  <img src="./Screenshot_2026-09-04_100328.png" alt="Compact query console" width="38%"/>
</div>


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Sensor composites and monitored zones

| Composite | Purpose |
|:---|:---|
| RGB True | Natural-colour view of the AOI |
| NIR False | Vegetation and land/water contrast |
| NDWI Water | Water bodies and flood extent |
| NDVI Veg | Canopy health and deforestation |
| SAR Radar | All-weather, cloud-penetrating imaging |
| Thermal | Heat signatures |

| Zone | Primary sensor | Use case |
|:---|:---|:---|
| Mumbai Port & Naval Dockyard | Cartosat-3 (0.28 m) | Vessel counting, port infrastructure |
| Brahmaputra River Basin | Sentinel-1/2 SAR | Flood inundation |
| Western Ghats Biosphere | Sentinel-2 MSI | Deforestation and canopy loss |
| SDSC-SHAR Sriharikota | Cartosat-3 | Launch-site monitoring |

**Example queries**

```text
Count all cargo and naval ships in this harbor area
Identify large container vessels docked along the eastern berths
Detect any fuel storage tanks and coastal support infrastructure
forest fires in Western Ghats
```


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Feasibility

<div align="center">
  <img src="./assets/feasibility.svg" alt="Technology readiness chart, 87% overall feasibility" width="100%"/>
</div>

### Risks and mitigation

| Risk | Severity | Mitigation |
|:---|:-:|:---|
| Cloud cover and data availability | High | Multi-sensor fallback: SAR when optical is clouded |
| Model accuracy (false positives, subtle changes) | High | Evidence-first grounding, confidence scoring, human verification flags |
| Optical–SAR cross-modal alignment | Medium | Phased validation approach |
| Limited validation data | Medium | Benchmark datasets, adapters planned |
| Geospatial uncertainty | Low | WGS84 / EPSG:4326 georeferencing checks |

An offline deterministic demo mode keeps the system demonstrable in any environment.


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## User flow

```mermaid
flowchart TD
    S([Start]) --> Q[Analyst submits a natural-language query]
    Q --> AOI[Select AOI and time range]
    AOI --> P[Engine parses intent and routes tools]
    P --> R[Run specialist tool / Optical–SAR fusion]
    R --> ST[Status: Processing → Verified]
    ST --> UP[Upload evidence map and metrics]
    UP --> C[Compute confidence score]
    C --> D{Confidence below threshold?}
    D -- Yes --> H[Decision maker reviews and acts]
    D -- No --> V[Mark as verified and notify user]
    H --> E([Verified insights and alerts])
    V --> E
    style D fill:#78350f,stroke:#fbbf24,color:#fff
    style E fill:#064e3b,stroke:#34d399,color:#fff
```


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Impact

| Who benefits | How |
|:---|:---|
| Government and disaster agencies | Faster flood and land-use monitoring |
| Urban and infrastructure authorities | Urban expansion and infrastructure tracking |
| Agriculture and environment agencies | Vegetation and water-resource monitoring |
| Researchers and GIS professionals | Analysis through natural language |
| Decision makers | Evidence-based insights and faster decisions |

**National alignment:** UN SDG 9, 11, 13, 15. Indigenously built AI for sovereign geospatial intelligence, supporting India's climate and disaster-resilience goals.


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Tech stack

| Layer | Technologies |
|:---|:---|
| Frontend | React, TypeScript, Tailwind CSS, Vite, Leaflet (MapLibre planned) |
| Backend | Python REST API, agentic tool-calling DAG |
| AI / ML | Gemini 2.5 VLM, YOLOv8, SSIM / CVA, NDWI / NDVI / NBR, SAR–Optical fusion, GeoChat / RemoteCLIP (RS-VQA) |
| Geospatial | GeoJSON, COG raster pipeline, GeoTIFF / GDAL, WGS84 (EPSG:4326) |
| Data and audit | Optical, SAR and benchmark datasets, MongoDB audit trail |


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## Getting started

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>

# frontend
npm install
npm run dev            # http://localhost:5173

# backend
pip install -r requirements.txt
python app.py
```

Keep API keys (for example the Gemini key) in a `.env` file and never commit it.


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


## References

| Paper | Used for |
|:---|:---|
| [VRSBench](https://arxiv.org/abs/2406.12384) | Single-image captioning, grounding and VQA baseline |
| [Change Detection Meets Visual Question Answering](https://arxiv.org/abs/2112.06343) | CDVQA task definition and fusion baseline |
| [Show Me What and Where has Changed?](https://arxiv.org/abs/2410.23828) | Pairing change answers with pixel-level masks |
| GeoChat: Grounded Large Vision-Language Model for Remote Sensing | Remote-sensing VLM reference |


<div align="center"><img src="./assets/divider.svg" alt="" width="100%"/></div>


<div align="center">

**Team ZeroOne** · Team ID 127589 · Atria Institute of Technology

<!-- Add team members:
| Name | Role | GitHub |
|---|---|---|
-->

</div>
