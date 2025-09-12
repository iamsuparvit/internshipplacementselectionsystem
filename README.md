# Internship Placement Selector

This project is a **Python Jupyter Notebook** (`.ipynb`) that assigns pharmacy students to internship sites based on their ranked preferences, shift (1 or 2), site seat availability, and sex requirements.  
It was designed to handle **separate shifts** (Shift 1 then Shift 2) with independent seat counts, and runs a **lottery** when sites are oversubscribed.

---

## Features
- Input from two CSV files:
  - **Students file** (`student_path`):
    ```
    student_name, student_id, sex, shift, rank 1, rank 2, ..., rank x
    ```
    - `sex`: `male` or `female`  
    - `shift`: `1` or `2`  
    - `rank i`: internship site code
  - **Drugstores file** (`drugstore_path`):
    ```
    code, branch, sex_require1, seat1, sex_require2, seat2
    ```
    - `sex_require1/2`: `male`, `female`, or `both` (for each shift)
    - `seat1/2`: number of seats for each shift

- Selection rules:
  - Process **Shift 1 completely first**, then **Shift 2**.  
  - Iterate ranks from most to least preferred.  
  - Skip choices if sex does not match site requirement.  
  - If applicants ≤ seats → all assigned.  
  - If applicants > seats → **random lottery per site/shift**.  
  - Unassigned after last rank → `Not selected`.

- Output:
  - A CSV file named with current datetime, suffixed `_output.csv`
  - Columns:
    ```
    student_name, student_id, rank_result, result
    ```
    - `rank_result`: rank number where student matched (1..x) or `0` if unassigned
    - `result`: site code or `Not selected`

- Summary statistics printed:
  - Number of matches at each rank
  - Number of unassigned students

- Verification cell (optional):
  - Checks that assignments respect seats, sex requirements, and student choices.

---

## Example

### Example `drugstore_path.csv`
```csv
code,branch,sex_require1,seat1,sex_require2,seat2
CEN01,Central Branch 1,both,2,both,2
CEN02,Central Branch 2,both,2,, 
CEN03,Branch 3,male,1,male,1
````

### Example `student_path.csv`

```csv
student_name,student_id,sex,shift,rank 1,rank 2,rank 3
Alice,610001,female,1,CEN01,CEN02,CEN03
Bob,610002,male,2,CEN03,CEN01,CEN02
Carol,610003,female,1,CEN02,CEN01,CEN03
```

---

## Usage

1. Clone this repo and open the notebook in Jupyter Lab/Notebook.
2. In **Cell 1 – Configuration**, set:

   * `x` = number of ranked choices (matches student file columns)
   * `student_path` = path to student CSV
   * `drugstore_path` = path to drugstore CSV
   * `output_path` = directory to save results
   * `random_seed` (optional) for reproducibility
3. Run all cells.
4. Results:

   * Output CSV will be saved in `output_path`.
   * Summary printed in the notebook.

---

## Verification

After running the selection, execute the **Verification Checks** cell to ensure:

* All assigned students get one of their selected ranks.
* No site exceeds its seat capacity.
* Sex requirements are respected.
* No student is assigned more than once.

---

## Requirements

* Python 3.8+
* Libraries:

  * pandas
  * numpy

Install with:

```bash
pip install pandas numpy
```

---

## License

MIT License
Created by Suparvit P., RxCU

---
