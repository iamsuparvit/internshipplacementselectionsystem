# Internship Placement Selection System

This project is an automated system designed to assign pharmacy students at Chulalongkorn University to internship sites (drugstores/hospitals). It allocates placements based on student preferences (ranked choices), shift availability, and specific site constraints such as seat capacity and gender requirements.

[Click here to view the PDF presentation](internshipPlacementSelectionSystem.pdf)

## Features

*   **Ranked-Choice Allocation**: Processes student preferences from Rank 1 to Rank X.
*   **Shift Management**: Handles multiple internship shifts (e.g., Shift 1, Shift 2).
*   **Constraint Enforcement**:
    *   **Seat Capacity**: Ensures sites do not exceed their maximum number of students per shift.
    *   **Gender Requirements**: Respects site-specific requirements (Male only, Female only, or Both).
*   **Fair Tie-Breaking**: Uses a random lottery system for oversubscribed sites within the same rank and shift.
*   **Validation & Verification**: Includes comprehensive checks for data integrity, capacity violations, and logic errors.
*   **Analysis**: Generates summary statistics and visualizations of the allocation results.

## Prerequisites

*   Python 3.x
*   Jupyter Notebook
*   Required Python libraries:
    *   `pandas`
    *   `numpy`
    *   `matplotlib`

## Input Files

The system expects two CSV files in the project directory:

### 1. Student Selection File (`student_selection.csv`)
Contains student details and their ranked choices.
*   **Columns**: `student_name`, `student_id`, `sex`, `shift`, `rank 1`, `rank 2`, ..., `rank x`
*   **Values**:
    *   `sex`: `Male`, `Female`
    *   `shift`: Integer (e.g., `1`, `2`)
    *   `rank x`: The code of the internship site.

Example (first 5 rows):

| student_name | student_id | sex | shift | rank1 | rank2 | rank3 | rank4 | rank5 |
|---|---:|---|---:|---|---|---|---|---|
| d4743cd5a0e8a401b83d139eb924235143cf5394a28730... | XXXXXXXXXX | male | 2 | FCN49 | FCN49 | FCN49 | FCN49 | FCN49 |
| ee5fa7606916cc9994d1c1a6f986f3b4926377c49d8923... | XXXXXXXXXX | male | 2 | FCN62 | FCN60 | FCN61 | FCN64 | FCN59 |
| 79cb28ef79b086da8546693c0c362f957d3211d403fa2b... | XXXXXXXXXX | male | 1 | BTS05 | HPT01 | BTS20 | BTS14 | BTS12 |
| 6e5cf96bde58559be4f0423fa9c3149eae506f78c3b862... | XXXXXXXXXX | female | 1 | FCN68 | FCN55 | PUR04 | FCN67 | FCN66 |
| e50488993ca3e7c428c8d21ccc546a588093234e6fb6d2... | XXXXXXXXXX | female | 2 | FCN41 | FCN32 | CEN03 | BTS15 | FCN31 |

### 2. Drugstore/Site File (`drugstore_path.csv`)
Contains details about internship sites, capacities, and requirements.
*   **Columns**: `code`, `branch`, `sex_require1`, `seat1`, ..., `sex_requireN`, `seatN` (where N is the number of shifts).
*   **Values**:
    *   `sex_require{i}`: `Male`, `Female`, `Both`
    *   `seat{i}`: Integer (number of available seats for shift `i`).

Example (first 5 rows):

| code | branch | sex_require1 | seat1 | sex_require2 | seat2 |
|---|---|---|---:|---|---:|
| CEN01 | 6 สโมสรวัฒนธรรมหญิง | both | 2.0 | both | 2.0 |
| CEN02 | 7 บุญมี ปุรุราชรังสรรค์ | both | 2.0 | NaN | NaN |
| CEN03 | 19 วงศ์สว่าง | male | 1.0 | male | 1.0 |
| CEN04 | 20 ป้อมปราบศัตรูพ่าย | NaN | NaN | both | 2.0 |
| CEN05 | 23 สี่พระยา | male | 1.0 | male | 1.0 |

## Usage

1.  **Prepare Data**: Ensure `student_selection.csv` and `drugstore_path.csv` are formatted correctly and placed in the project folder.
2.  **Configure**: Open `internshipPlacementSelectionSystem.ipynb`. In **Cell 1**, you can adjust:
    *   `x`: Number of ranks per student (should match the student selection file)
    *   `SHIFT_ORDER`: Indicates how many shifts will be processed. If there is only one shift, or if you prefer to manually separate each shift, set this value to 1. In that case, ensure that the shift column in the student selection file is also set to 1.
    *   `random_seed`: Set a seed for reproducible results (optional).
3.  **Run**: Execute the notebook cells from top to bottom.
4.  **Results**:
    *   The system will generate an output CSV in the `output/` directory named with the current timestamp (e.g., `YYYYMMDD_HHMMSS_output.csv`).
    *   A summary of remaining seats is also saved as `YYYYMMDD_HHMMSS_remaining_seats.csv`.

## Allocation Logic

1.  **Normalization**: All inputs are normalized (case-insensitive, space trimming).
2.  **Deduplication**: Duplicate site codes within a single student's rank list are removed (keeping the highest rank).
3.  **Allocation Loop**:
    *   Iterate through **Shifts** (1 to `SHIFT_ORDER`).
    *   Iterate through **Ranks** (1 to `x`).
    *   Identify eligible candidates for each site who have not yet been assigned.
    *   Check constraints (Sex, Shift, Remaining Seats).
    *   **Lottery**: If candidates > available seats, a random selection is performed.
4.  **Unassigned Students**: Students not matched after all ranks are processed are marked as "Not selected".

## Output

The output file contains the original student data (`student_name` and `student_id`) plus:
*   `rank_result`: The rank number they were assigned (0 if not selected).
*   `result`: The code of the assigned site (or "Not selected").
*   `branch`: The branch name of the assigned site (empty if not selected).

Example (first 5 rows):

| student_name | student_id | rank_result | result | branch |
|---|---:|---:|---|---|
| d4743cd5a0e8a401b83d139eb924235143cf5394a28730... | XXXXXXXXXX | 0 | Not selected |  |
| ee5fa7606916cc9994d1c1a6f986f3b4926377c49d8923... | XXXXXXXXXX | 1 | FCN62 | บ้านดู่ |
| 79cb28ef79b086da8546693c0c362f957d3211d403fa2b... | XXXXXXXXXX | 1 | BTS05 | Q House Lumpini |
| 6e5cf96bde58559be4f0423fa9c3149eae506f78c3b862... | XXXXXXXXXX | 1 | FCN68 | ตลาดหนองดอก ลำพูน |
| e50488993ca3e7c428c8d21ccc546a588093234e6fb6d2... | XXXXXXXXXX | 1 | FCN41 | คาลเท็กซ์-ราชพฤกษ์ |

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---
*Developed for Chulalongkorn University Pharmacy Internship Placement.*
