# Eye Tracking Analysis: Cycling Scenarios (Master’s HCI, Bauhaus-Universität Weimar)

This project analyzes eye-tracking data collected from participants riding two types of bicycles:
- Normal bike
- Electric cargo bike

Participants completed rides across varying traffic and speed conditions, and their gaze data was recorded using an eye-tracking device. The analysis focuses on where participants look (left, centre, right), how long they focus in each area, and how focus changes throughout the journey.

The repository contains a single Jupyter notebook (`Project_Eye.ipynb`) that loads the dataset from a zip archive, computes key metrics, and generates visualizations.

## Project Goals
- Quantify participant focus across areas of interest (left, centre, right).
- Compare focus patterns between traffic and speed scenarios.
- Measure focus durations and frequencies.
- Analyze how focus shifts over the start, middle, and end of a journey.
- Summarize average gaze coordinates per participant.

## Dataset
- Source: Eye tracking device recordings during cycling tasks.
- Modalities: Normal bike vs Electric cargo bike.
- Conditions considered in analysis (inferred from code):
  - Traffic: High Traffic, Low Traffic
  - Speed: High Speed, Low Speed
- Structure expected by the analysis code after extraction:
  - `csvFiles.zip` contains CSV files organized as:
    - `High Traffic/High Speed/participant1.csv`, `participant2.csv`, `participant3.csv`, `participant4.csv`
    - `High Traffic/Low Speed/...`
    - `Low Traffic/High Speed/...`
    - `Low Traffic/Low Speed/...`
- Each participant’s CSV includes columns such as (typical/inferred):
  - `gaze_timestamp`: numeric timestamp used to segment the journey into start/middle/end
  - `norm_pos_x`, `norm_pos_y` or similar gaze coordinates (used to derive “focus” area)
  - Other eye-tracking fields from the recording system

## Notebook Overview (`Project_Eye.ipynb`)
The notebook was designed to run in Google Colab (badge is included in the first markdown cell) but can also run locally.

Key steps implemented in the notebook:
1. Data extraction
   - Unzips `csvFiles.zip` into a working directory (e.g., `/content/data2/` in Colab).
2. Data loading
   - `load_data(folder_path)` builds a nested dictionary in the shape:
     - `{ scenario: { participant_id: pandas.DataFrame } }`
     - Where scenario is `"High Traffic/High Speed"`, `"High Traffic/Low Speed"`, `"Low Traffic/High Speed"`, `"Low Traffic/Low Speed"`.
3. Focus computation
   - `calculate_focus(df)` assigns each row a categorical focus label (`left`, `centre`, `right`) based on gaze position/thresholds.
   - A constant `FOCUS_THRESHOLD` controls centre vs side classification.
4. Metrics & summaries
   - Centre focus counts per participant and scenario.
   - Focus mean and standard deviation using mapping `{left: 0, centre: 1, right: 2}`.
   - Differences across scenarios (traffic/speed) using aggregated means.
5. Journey segmentation
   - Segments the ride into `start`, `middle`, `end` using `pd.cut` over `gaze_timestamp`.
   - Computes focus distributions across journey phases for each participant and scenario.
6. Visualizations
   - `plot_focus_duration(data)`: total time spent focusing left/centre/right (by timestamp deltas or counts).
   - `plot_focus_count(data)`: frequency of focus occurrences left/centre/right.
   - `plot_average_focus(data)`: average gaze coordinates per participant.

## Features
- Automated dataset extraction from a zip archive.
- Scenario-aware data loader across traffic and speed conditions.
- Derivation of categorical focus (left/centre/right) from raw gaze positions.
- Participant-level summaries: counts, means, standard deviations of focus.
- Journey phase analysis (start/middle/end) using timestamps.
- Multiple plots for duration, frequency, and average focus coordinates.
- Simple configuration via constants (e.g., focus threshold).

## Technology Stack
- Python 3.x
- Jupyter Notebook / Google Colab
- Libraries:
  - pandas
  - numpy
  - matplotlib
  - seaborn
  - zipfile (standard library)
  - os (standard library)

## How to Run (Local)
1. Set up a Python environment (optional but recommended):
   - Create and activate a virtual environment.
2. Ensure the dataset zip (`csvFiles.zip`) is present at the project root.
3. Open `Project_Eye.ipynb` in Jupyter or PyCharm’s notebook support.
4. Update the data paths in the first cells if not using Colab. For local runs, set paths like:
   - Extraction target: `./data/`
   - Data folder path: `./data/csvFiles/`
5. Run the notebook cells in order.

Example local adjustments inside the notebook:
- Replace Colab paths such as `/content/data2/csvFiles/` with a local path (e.g., `./data/csvFiles/`).
- Ensure the `load_data(folder_path)` points to the extracted `csvFiles` folder.

## How to Run (Google Colab)
- Click the “Open in Colab” badge in the notebook, or upload `Project_Eye.ipynb` and `csvFiles.zip` to Colab.
- Ensure the extraction cell points at the uploaded zip, e.g.:
  - `zipfile.ZipFile('/content/csvFiles.zip', 'r')`
  - Extract to `/content/data2/`, then set `data_folder_path = "/content/data2/csvFiles/"`.
- Run all cells.

## Expected Outputs
- Printed tables summarizing:
  - Centre focus counts per participant and scenario.
  - Focus means/standard deviations and differences across scenarios.
  - Journey phase focus distributions (start/middle/end).
- Plots showing:
  - Focus duration per area (left/centre/right) by participant/scenario.
  - Focus frequency per area.
  - Average focus coordinates.

## Notes & Assumptions
- Folder names and participant file naming must match the expected pattern used in `load_data`.
- `calculate_focus` relies on gaze coordinate normalization and a `FOCUS_THRESHOLD`; adjust if your device outputs differ.
- If any CSVs contain missing columns, the code drops columns with all-NA values (`df.dropna(axis='columns', inplace=True)`).
- The notebook was authored for a Master’s project in Human-Computer Interaction at Bauhaus-Universität Weimar.

## Repository Structure
- `Project_Eye.ipynb`: Main analysis notebook (Colab-ready).
- `csvFiles.zip`: Eye-tracking dataset zip archive.
- `README.md`: This document.

## Troubleshooting
- Path errors: Verify the unzip destination and `data_folder_path` match.
- Missing files: Confirm `csvFiles.zip` contains the scenario/participant folders and CSVs.
- Plotting issues: Ensure matplotlib/seaborn are installed and the notebook kernel has these packages.

## License
- No explicit license included. If you intend to reuse or publish, please add an appropriate license and acknowledge the project’s academic context.
