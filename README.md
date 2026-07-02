# Lipid Compound / Lipid Droplet Analysis

Notebook-driven image analysis for lipid droplet quantification in cell images.

**Notebook:** [lipid_compound_analysis_062926.ipynb](lipid_compound_analysis_062926.ipynb)

## What This Does

This workflow segments cells, nuclei, cytoplasm, and lipid droplets from multi-channel .czi microscopy images, saves masks, and exports per-cell measurements to CSV.

The notebook processes folders such as `52726 images`, `60326 images`, and the other date-stamped folders listed in `IMAGE_FOLDER_NAMES`.

## Quickstart

This repository only includes the notebook. Start from a fresh clone, create the expected folders, then put your .czi files into the right place.

1. Open a terminal in the lipid compound analysis folder.
2. Create the folder structure the notebook expects:

   ```bash
   mkdir -p images Results Results/masks
   mkdir -p "images/ (image folder name) " 
   ```

3. Copy each .czi file into the matching folder under `images/`. If you want to test with just one folder, that is fine too.
4. Install the Python dependencies if needed:

   ```bash
   pip install cellpose numpy scikit-image matplotlib pandas czifile scipy
   ```

5. Open [lipid_compound_analysis_062926.ipynb](lipid_compound_analysis_062926.ipynb) and run it top to bottom.
6. Check `Results/` for the CSV output and `Results/masks/` for saved masks.

## Requirements

- Python 3.10 or newer
- `cellpose`
- `numpy`
- `scikit-image`
- `matplotlib`
- `pandas`
- `czifile`
- `scipy`

GPU support is optional. If a compatible GPU is available, keep `USE_GPU = True` in the notebook.

## Inputs

- Multi-channel `.czi` files in the `images/<date> images/` folders
- Notebook parameters near the top of [lipid_compound_analysis_062926.ipynb](lipid_compound_analysis_062926.ipynb)

The notebook looks for each folder listed in `IMAGE_FOLDER_NAMES` and writes a separate CSV for each one.

## Outputs

- Per-image masks in `Results/masks/<date>/` as `.npy` files
- Inline QC figures for each image
- Per-folder CSV output in `Results/` named like `<date>_lipid_compound_lipid_droplet_analysis.csv`

## Workflow

1. Load the orange and blue channels from each .czi file.
2. Normalize the channels for segmentation and display.
3. Segment whole cells with Cellpose cyto3.
4. Segment nuclei with the Cellpose nuclei model.
5. Recover missed cells from nuclei when needed.
6. Detect lipid droplets in the orange channel.
7. Save masks and write the per-cell CSV for each folder.

## Main Parameters

Tune these near the top of the notebook if your images need different settings:

- Whole-cell segmentation: `CYTO_DIAMETER`, `CYTO_FLOW_THRESH`, `CYTO_PROB_THRESH`, `CELL_MIN_AREA`
- Nucleus segmentation: `NUC_DIAMETER`, `NUC_FLOW_THRESH`, `NUC_PROB_THRESH`, `NUC_MIN_AREA`
- Hybrid cell fill: `CELL_FILL_FG_THRESH`, `CELL_GROW_DIST`
- Lipid droplets: `LIPID_TOPHAT_RADIUS`, `LIPID_MIN_SIZE`

## Tips

- If Cellpose is unstable on GPU, set `USE_GPU = False`.
- If cells are being split, increase `CYTO_DIAMETER` a bit.
- If nuclei are missed, lower `NUC_PROB_THRESH` or `NUC_MIN_AREA`.
- If lipid masks are too noisy, raise `LIPID_TOPHAT_RADIUS` or `LIPID_MIN_SIZE`.

## Troubleshooting

- If the notebook cannot find images, check that the folder names match `IMAGE_FOLDER_NAMES` exactly.
- If .czi loading fails, verify that `czifile` is installed and the files are readable.
- If the output CSV is empty, check whether the segmentation step is finding cells.

## Notes

This notebook is part of the Kara Lab analysis workflow and writes outputs to the local `Results/` tree.
