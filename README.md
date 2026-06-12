# badminton-motion-analysis

Multi-camera 3D pose reconstruction and metabolic load analysis for badminton players, including MET estimation, player load, and swing detection.

## Pipeline

```
multi-camera video
      ↓
top_down_jsonl.py        # 2D pose → 3D triangulation → Kalman filter → trajectory CSV
      ↓
detect_swing_from_csv.py # elbow angular velocity → swing detection
      ↓
badminton_dynamic_met_estimation.py  # MET / calorie / Player Load / MAD
```

## Files

| File | Description |
|------|-------------|
| `top_down_jsonl.py` | Main pipeline: reads pose JSONL, fills missing keypoints (Akima/PCHIP), triangulates 3D, applies Kalman filter, estimates player height, outputs trajectory CSV |
| `detect_swing_from_csv.py` | Detects swing frames from elbow angular velocity |
| `badminton_dynamic_met_estimation.py` | Computes dynamic MET, calories, Player Load, and MAD from trajectory CSV |
| `skeleton_util.py` | Shared constants: COCO keypoint enum, skeleton connections, court coordinates |

## Player Info Configuration

In `top_down_jsonl.py`:
```python
PLAYER1_HEIGHT_M = None   # e.g. 1.78 (m), None → auto-estimate from keypoints
```

In `badminton_dynamic_met_estimation.py`:
```python
PLAYER1_WEIGHT_KG = None  # e.g. 68.0 (kg), None → fallback 70 kg
PLAYER1_HEIGHT_M  = None  # e.g. 1.78 (m),  None → read from CSV estimate
```

## References

### Badminton Physiology & Energy Expenditure
- Phomsoupha, M., & Laffaye, G. (2015). The science of badminton: Game characteristics, anthropometry, physiology, visual fitness and biomechanics. *Sports Medicine, 45*(4), 473–495.
- Faude, O., Meyer, T., Rosenberger, F., Fries, M., Huber, G., & Kindermann, W. (2007). Physiological characteristics of badminton match play. *European Journal of Applied Physiology, 100*(4), 479–485.
- Ainsworth, B. E., et al. (2011). 2011 Compendium of Physical Activities. *Medicine & Science in Sports & Exercise, 43*(8), 1575–1581.
- Ainsworth, B. E., et al. (2024). 2024 Adult Compendium of Physical Activities. *Journal of Sport and Health Science.*

### Player Load Methodology
- Boyd, L. J., Ball, K., & Aughey, R. J. (2011). The reliability of MinimaxX accelerometers for measuring physical activity in Australian football. *International Journal of Sports Physiology and Performance, 6*(3), 311–321.
- Gabbett, T. J. (2016). The training-injury prevention paradox: should athletes be training smarter and harder? *British Journal of Sports Medicine, 50*(5), 273–280.

### 3D Pose Estimation
- Sun, K., Xiao, B., Liu, D., & Wang, J. (2019). Deep High-Resolution Representation Learning for Visual Recognition. *IEEE TPAMI.*
- Pavllo, D., Feichtenhofer, C., Grangier, D., & Auli, M. (2019). 3D human pose estimation in video with temporal convolutions and semi-supervised training. *CVPR.*
- Hartley, R., & Zisserman, A. (2004). *Multiple View Geometry in Computer Vision* (2nd ed.). Cambridge University Press.

### Body Segment Parameters
- De Leva, P. (1996). Adjustments to Zatsiorsky-Seluyanov's segment inertia parameters. *Journal of Biomechanics, 29*(9), 1223–1230.
  > Used for forearm+hand mass ratio (0.0223 × body mass), forearm length (0.146 × height), and hand length (0.108 × height).

### Upper Limb Biomechanics
- Rambely, A. S., Osman, N. A. A., Usman, J., & Abas, W. A. B. W. (2005). The contribution of the upper limb joints in the badminton smash. *ISBS Conference Proceedings.*
- Velardo, C., Dugelay, J. L., & Paleari, M. (2014). Weight estimation from visual body appearance. *IEEE IPTA.*
