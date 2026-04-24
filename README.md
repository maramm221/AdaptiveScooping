

# Feeding Project Setup and Run Guide

Geometry-aware assistive feeding simulation in Unity (RCareWorld + Kinova Gen3 + Obi grains), with:
- **Baseline pipeline** (fixed primitives)
- **Adaptive pipeline** (perception + bite candidates + IK refinement)

This README is a practical "from zero to running trials" guide.

---

## 1) Prerequisites

### Required software
- **Unity Hub**
- **Unity Editor `2022.3.34f1`** (exact version recommended)
- **Python 3.9+** (for OMPL server)
- **Git + Git LFS** (if cloning from git)

### Required Unity-side stack (in project)
- RCareWorld / RCareUnity project content
- Obi (already used by this repo setup)

### Required Python module
`Common/ompl_server.py` imports:
- `numpy`
- `ompl` (Python OMPL bindings)

If `import ompl` fails, install OMPL Python bindings in your environment (commonly via conda-forge in lab setups).

---

## 2) Get the Project Files

You can use either method.

### Option A: Download ZIP
1. Download ZIP from GitHub.
2. Extract to a path without special permissions (for example `D:\Feeding`).

### Option B: Clone with Git
```bash
git clone <YOUR_REPO_URL> Feeding
cd Feeding
git lfs install
git lfs pull
git submodule update --init --recursive
```

> If LFS is not pulled, large assets may appear missing in Unity.

---

## 3) Open the Project in Unity

1. Open **Unity Hub**.
2. Click **Add project from disk** and select the repo folder (for example `D:\Feeding`).
3. Ensure Unity version is **2022.3.34f1**.
4. Open and let the first import finish (this can take several minutes).

---

## 4) Run OMPL Server (Python)

Open a terminal at repo root and create/activate an environment.

### Windows (PowerShell)
```powershell
cd D:\Feeding
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install numpy
```

If your environment does not already include OMPL bindings, install the Python OMPL package available in your setup (lab environments often use conda-forge).

Run server:
```powershell
python .\Common\ompl_server.py
```

Expected output:
```text
OMPL server listening on 127.0.0.1:9001
```

Keep this terminal open while running Unity trials.

---

## 5) Scene Setup Checklist (Unity)

Open your feeding scene (or the RCare example scene you use for feeding) and verify references.

### Core components
- `KinovaJointController`
- `PlateFoodPerception`
- `CameraCapture` (wrist depth)
- `ObiGrainFood`
- `TorqueSensor` (optional but recommended)
- `EvaluationMetricsLogger` (for CSV outputs)
- `FeedingSystemPlayCoordinator` (single Play switch)

### Pipeline managers
- Baseline: `BaselineSystem/FeedingPipelineManager`
- Adaptive: `AdaptiveSystem/AdaptiveFeedingPipelineManager`

### Dish objects
- Plate root assigned to `plateDish`
- Bowl root assigned to `bowlDish`
- Only one is active per run (coordinator handles this)

### OMPL client
On `OmplClient`:
- `host = 127.0.0.1`
- `port = 9001`

---

## 6) Choose What to Run (Coordinator)

Use `FeedingSystemPlayCoordinator` in Inspector:
- `runSystemOnPlay`: **true**
- `activeSystem`: `Baseline` or `Adaptive`
- `activeDish`: `Plate` or `Bowl`

This coordinator also toggles trial runner auto-start for the selected system.

---

## 7) Configure Trials and Scoops

### Number of trials
- Baseline: set `trials` on `BaselineTrialRunner`
- Adaptive: set `trials` on `AdaptiveTrialRunner`

### Scoops per trial
- **Adaptive**: `AdaptiveFeedingPipelineManager` uses internal `MaxScoops` constant.
- **Baseline**: `FeedingPipelineManager` uses internal `MaxScoops` constant.

Set both to your target protocol (for example 5 trials x 5 scoops).

---

## 8) Play and Generate Logs

1. Ensure OMPL server is running.
2. Press **Play** in Unity.
3. Let trial runner complete.
4. Check outputs in:
   - `EvaluationLogs/` (CSV metrics)
   - `EvaluationLogs/BiteSegmentation/` (candidate segmentation images, if enabled)

---

## 9) Troubleshooting

### A) Arm does not reach mouth pose fully
- Confirm latest carry/mouth enforcement patches are present.
- Check console for safety-stop/collision abort logs.
- Verify coordinator selected expected system and dish.

### B) OMPL not responding
- Confirm server terminal shows `listening on 127.0.0.1:9001`.
- Confirm Unity `OmplClient` host/port match.
- Confirm Python env has `ompl` module available.

### C) Zero grains collected
- Validate spoon collider + ObiCollider assignment.
- Ensure active dish has correct Obi solver/emitter pair.
- Ensure scoop path physically passes through food layer.
- Run Obi validation tools/checks in scene components.

### D) Slow or laggy arm
- Reduce particle count / solver load.
- Reduce depth scan load while debugging.
- Allow full first import and avoid disk quota issues.

---

## 10) Suggested Evaluation Protocol (Repo Default Example)

- Conditions:
  - Baseline + Plate
  - Baseline + Bowl
  - Adaptive + Plate
  - Adaptive + Bowl
- Repetitions:
  - 5 trials per condition
  - 1 or 5 scoops per trial (keep consistent across conditions)

Use same scene and parameter settings across all cells for fair comparison.

---

## 11) Results Section Template (for GitHub)

You can paste this into the same README after you run experiments.

### 11.1 Representative Screenshots

#### Baseline
![Baseline run screenshot](docs/images/baseline_run.png)

#### Adaptive
![Adaptive run screenshot](docs/images/adaptive_run.png)

#### Bite Segmentation
![Bite segmentation debug](docs/images/bite_segmentation_example.png)

> Replace paths with your actual image locations in the repo.

### 11.2 Summary Table (fill from CSV)

| Mode | Dish | Trials | Scoops/Trial | Completion Rate | Mean Time/Bite (s) | Notes |
|---|---:|---:|---:|---:|---:|---|
| Baseline | Plate |  |  |  |  |  |
| Baseline | Bowl |  |  |  |  |  |
| Adaptive | Plate |  |  |  |  |  |
| Adaptive | Bowl |  |  |  |  |  |

### 11.3 Key Observations

- Observation 1:
- Observation 2:
- Observation 3:

---

## 12) Important Scope Notes

- This is currently a **simulation-only** evaluation.
- Some contact-quality metrics depend on scene-specific spoon/particle interaction quality.
- Use completion/time/perception-alignment metrics as primary evidence if grain retention instrumentation is unstable.

---

## 13) Related Docs in This Repo

- `README_PIPELINES.md` - baseline vs adaptive notes
- `SETUP_UNITY.txt` - detailed Unity + Obi setup checklist
- `Common/ompl_server.py` - OMPL Python server used by `OmplClient`

