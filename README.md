

# Feeding Project

This README is for the **zipped source-code submission 21184729_code.zip** (Unity setup + run instructions).
The appendix artifacts are provided in a **Github repository**:

- Appendix repository: `[https://github.com/maramm221/AdaptiveScooping]`
  
Contents in appendix repo: final baseline/adaptive CSV evaluation files and generated plate/bowl run images.

## 1) Install the correct Unity version
- Install **Unity Hub**
- Install **Unity Editor `2022.3.34f1`**

## 2) Download and extract code
1. Download `21184729_code.zip`
2. Extract it (example: `(root)/21184729_code`)

## 2.5) Prepare Unity Library cache (Linux)

> **Check your free disk space first:**
> ```bash
> df -h ~
> ```

**If you have 20 GB or more free** — skip this step entirely. Unity will create the `Library` folder automatically when you open the project.

**If you have less than 20 GB free** — redirect the Library cache to `/tmp` to avoid filling your disk:

```bash
mkdir -p /tmp/RCareUnity/Library
ln -s /tmp/RCareUnity/Library (root)/21184729_code/RCareUnity/Library
```

> **Important:** `/tmp` is cleared on every reboot. If Unity fails to open the project after a restart, re-run the two commands above before launching.

**To undo the redirect later** (once you have enough space):
```bash
rm (root)/21184729_code/RCareUnity/Library
```
Then reopen the project and Unity will rebuild the Library folder in place.
## 3) Open project in Unity
1. Open Unity Hub
2. Click **Add project from disk**
3. Select the extracted folder path:
   - `...\21184729_code\RCareUnity`
4. Open with Unity `2022.3.34f1`
5. Wait for import/compile to finish

## 4) Open the feeding scene
In Unity Project window, navigate to '(root)\21184729_code\RCareUnity\Assets\RCareCommon\Example Scenes' and open the 'Feeding' scene.

## 5) Start OMPL server (terminal)
Open terminal in project root and run:

### Linux
```bash
cd (root)/21184729_code/RCareUnity/Assets/RCareCommon/Scripts/Feeding/Common/
python3 -m venv .venv
source .venv/bin/activate
pip install ompl numpy pybullet
python3 ompl_server.py
```

### Windows PowerShell
```powershell
cd (root)\21184729_code\RCareUnity\Assets\RCareCommon\Scripts\Feeding\Common\
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install ompl numpy pybullet
py ompl_server.py
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

> **Defaults:** 3 scoops per trial, 3 trials per play session. These are configurable in the Unity Inspector on the respective trial runner components.

## 7) Press Play
- Go back to Unity
- Press **Play**
- The selected system/dish will run

## 8) Where outputs are saved
All outputs are saved under `(root)/21184729_code/RCareUnity/EvaluationLogs/`, with the following subdirectories:
- `AdaptiveDishGeometry/`
- `BaselineFoodHeight/`
- `BiteSegmentation/` 

---

## Notes
- If OMPL is not running, planned moves may fail or fall back.
- If you get Python error `No module named ompl`, install OMPL Python bindings in that environment.
- Baseline mode anchors dish geometry to the collider/renderer bounds; adaptive mode estimates geometry from the wrist-camera sweep.
- **If any Inspector field appears empty or unassigned anywhere in the project:** Click the circle icon next to the field and select the matching object from the list, or type the value directly. In most cases only one valid option will be available.
