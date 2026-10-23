# WUR-AV-Bench dataset

Labels, evaluation images and baseline results of WUR-AV-Bench: A Benchmark for Active Vision in Complex Greenhouse Environments.

The evaluation code is in two repositories:

- [wur-av-bench-cocoeval](https://github.com/TAW-V/wur-av-bench-cocoeval): background-aware detection and 2D pose evaluation (Tasks I and II)
- [wur-av-bench](https://github.com/TAW-V/wur-av-bench): 3D and 5D node reconstruction evaluation and annotation tools (Tasks III and IV)

The viewpoint data of the 25 plants (RGB images, point clouds and camera poses) is too large for this repository and is published separately on 4TU.ResearchData. Link in `TaskIII_and_TaskIV/camera_viewpoint_dataset.txt`.

## Contents

```
TaskI_and_TaskII/
├── evaluation/
│   ├── images/                             352 RGB images
│   ├── gt_labels/
│   │   ├── labels_backgroundaware.json     ground truth with foreground and background classes
│   │   └── labels.json                     same annotations, background objects labelled as normal objects
│   └── baseline_predictions/
│       └── coco_predictions.json           predictions of the baseline detector (masks, boxes and keypoints)
└── train/
    ├── images/                             images of other tomato plants, used to train the baseline detector
    ├── annotations.json                    COCO labels: masks of tomatoes, main stems, peduncles and petioles,
    │                                       with six keypoints per peduncle and petiole
    └── LICENCE
TaskIII_and_TaskIV/
├── gt/                                     one CSV per plant with the annotated nodes
├── baseline_runs/
│   ├── task3/{active,random}/              baseline predictions, 20 runs per plant
│   └── task4/{active,random}/
├── detection_filter_params.json            detection filter regions per plant
├── camera_intrinsics.txt                   intrinsics of the colour camera
├── point_cloud_layout.txt                  file formats of the viewpoint data
└── camera_viewpoint_dataset.txt            link to the viewpoint data
```

## Tasks I and II

The labels and predictions are in COCO format. Every node has a segmentation mask and two keypoints: `Edge`, the node point where the peduncle or petiole attaches to the stem, and `Pt`, a point that gives its direction. In `labels_backgroundaware.json` the class IDs end in 0 for foreground and in 1 for background objects:

```
10000  peduncle             10001  peduncle_background
20000  petiole              20001  petiole_background
```

Predictions use the foreground IDs. See [wur-av-bench-cocoeval](https://github.com/TAW-V/wur-av-bench-cocoeval) for the background-aware evaluation and the keypoint criterion.

## Tasks III and IV

`gt/plantN.csv` has one row per annotated node, with the columns `ID, x, y, z, qx, qy, qz, qw, class, occlusion_level, plant_complexity_level`. Positions are in metres and the poses follow the ROS convention (+X forward), as expected by `wur-av-bench eval`.

`baseline_runs/taskN/{active,random}/plantN_runKKK.json` holds one run of a baseline on one plant. The file is a list with one entry per selected viewpoint; entry `k` contains all nodes found after `k` viewpoints, each with `x, y, z, qx, qy, qz, qw` and `class`. These are the files evaluated in the paper.

`detection_filter_params.json` gives for each plant three cylinders around the main stem (end points `pointa` and `pointb` and `radius`, in metres, in the same frame as the ground truth). Detections outside these regions belong to neighbouring plants and can be removed in each viewpoint. The regions are meant only for this local filtering and should not be used as prior information by active vision methods.

`camera_intrinsics.txt` and `point_cloud_layout.txt` describe the camera and the file formats of the viewpoint data (colour images, organized point clouds and camera poses).

## License

The data in this repository is licensed under the Creative Commons Attribution 4.0 International licence (CC BY 4.0), see [LICENSE](LICENSE).
