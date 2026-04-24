

# Feeding Project - Quick Start (Professor Guide)

This is the shortest path to run the project.

## 1) Install the correct Unity version
- Install **Unity Hub**
- Install **Unity Editor `2022.3.34f1`**

## 2) Download and extract code
1. Download `21184729_code.zip`
2. Extract it (example: `D:\21184729_code`)

## 3) Open project in Unity
1. Open Unity Hub
2. Click **Add project from disk**
3. Select the extracted folder path:
   - `...\21184729_code\RCareUnity`
4. Open with Unity `2022.3.34f1`
5. Wait for import/compile to finish

## 4) Open the feeding scene
In Unity Project window, navigate to the feeding scene and open it.

## 5) Start OMPL server (terminal)
Open terminal in project root and run:

### Windows PowerShell
```powershell
cd D:\21184729_code
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install numpy
python .\Common\ompl_server.py
```

Expected message:
```text
OMPL server listening on 127.0.0.1:9001
```

Keep this terminal open.

## 6) Configure what to run
In the scene, select **FeedingSystemPlayCoordinator** and set:
- `runSystemOnPlay = true`
- `activeSystem = Baseline` or `Adaptive`
- `activeDish = Plate` or `Bowl`

## 7) Press Play
- Go back to Unity
- Press **Play**
- The selected system/dish will run

## 8) Where outputs are saved
- CSV logs: `EvaluationLogs/`
- Bite segmentation images (if enabled): `EvaluationLogs/BiteSegmentation/`

---

## Notes
- If OMPL is not running, planned moves may fail or fall back.
- If you get Python error `No module named ompl`, install OMPL Python bindings in that environment.

