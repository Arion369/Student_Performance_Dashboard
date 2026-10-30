# 🧒 The Student Dashboard Code, Explained Like You're in 5th Grade

This guide walks through **every line** of `Mini_Project_1.ipynb`, using simple words and everyday examples.

---

## 🍳 The big picture first

Imagine your teacher gives you a **class register** (a big table of marks) and says:
*"Find mistakes, fix them, work out who's best, draw pictures of the results, and make a poster I can open on any computer."*

Our code does exactly that, in this order:

| Step | Kitchen version | What the code does |
|---|---|---|
| 1 | Get your tools out | Load the Python libraries |
| 2 | Get the ingredients | Read the CSV file (the marks table) |
| 3 | Wash the vegetables | Clean the data (fix `Null` and `0`) |
| 4 | Cook | Work out Total, Average, Grade and Rank |
| 5 | Taste and describe it | Statistics and insights |
| 6 | Decorate the plate | Charts and pictures |
| 7 | Serve it | Build the HTML dashboard and save the files |

---

## 📖 Mini dictionary (read this first!)

| Word | What it means, simply |
|---|---|
| **Library** | A toolbox someone else built that we can borrow. `pandas` is a toolbox for tables; `matplotlib` is a box of crayons for drawing charts. |
| **Variable** | A labelled box that holds something. `CSV_PATH = "student_performance.csv"` means "put this file name in a box called CSV_PATH". |
| **String** | Text inside quotes, like `"Alice"`. |
| **List** `[ ]` | A row of boxes in order: `["Python", "SQL", "Power BI", "AI"]`. |
| **Dictionary** `{ }` | A list of word → meaning pairs, like a real dictionary: `{"Python": "#4F46E5"}` means "Python's colour is #4F46E5". |
| **Tuple** `( )` | Like a list, but you can't change it once it's made. |
| **Function** (`def`) | A **recipe** with a name. You write it once, then "cook" it whenever you want by calling its name. |
| **`return`** | The recipe hands back the finished dish. |
| **DataFrame** (`df`) | A table inside Python, just like an Excel sheet: rows are students, columns are subjects. |
| **Column** | One vertical strip of the table, e.g. all the Python marks. |
| **Loop** (`for`) | "Do this same thing for every item." Like a teacher checking each student's homework one by one. |
| **`if` / `else`** | A decision: "IF it's raining, take an umbrella, ELSE wear sunglasses." |
| **`NaN`** | "Not a Number": an empty box. That's how pandas shows a missing value. |
| **`print()`** | Show a message on the screen. |
| **`display()`** | Show a table or picture nicely in the notebook. |
| **`#` comment** | A note for humans. Python ignores everything after `#` on a line. |
| **f-string** `f"..."` | Text with holes you fill in: `f"Hi {name}"` becomes `Hi Alice` if name is Alice. |

---

## Section 1 – Setup (getting the toolboxes out)

```python
import json
import os
import sys
import warnings
from datetime import datetime
```
- `import json`: borrow the tool that writes data in **JSON** format (a text format web pages understand). We use it later to send our data to the HTML dashboard.
- `import os`: tools for the computer's **files and folders**, like making a folder or checking whether a file exists.
- `import sys`: tools for asking about **Python itself**. We use it to find out "Am I running in Google Colab?"
- `import warnings`: tools to control **warning messages** (little yellow "be careful" notes).
- `from datetime import datetime`: borrow the **clock and calendar** tool, so we can write today's date and time on the dashboard.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import plotly
import plotly.express as px
import plotly.graph_objects as go
import plotly.io as pio
import altair as alt
import ipywidgets as widgets
from IPython.display import display, HTML, Markdown
```
- `numpy as np`: a super-fast **calculator** for lots of numbers. `as np` gives it a short nickname so we type `np` instead of `numpy`.
- `pandas as pd`: the **table toolbox** (our Excel inside Python). Nickname `pd`.
- `matplotlib.pyplot as plt`: the **basic crayons** for drawing charts.
- `seaborn as sns`: **fancy crayons** that make matplotlib charts prettier with less work.
- `import plotly`: the **interactive chart** toolbox (charts you can hover over and zoom into).
- `plotly.express as px`: the **quick** way to make plotly charts in one line.
- `plotly.graph_objects as go`: the **detailed** way to build plotly charts piece by piece, like LEGO.
- `plotly.io as pio`: plotly **settings** (theme, where charts show up).
- `altair as alt`: another chart toolbox, good at **linked charts** (select in one chart and another chart changes).
- `ipywidgets as widgets`: **buttons, dropdowns and sliders** inside the notebook.
- `from IPython.display import ...`: tools to **show** things nicely: `display` (show anything), `HTML` (show a web-page piece), `Markdown` (show formatted text).

```python
IN_COLAB = "google.colab" in sys.modules
```
- Asks: "Is the Google Colab toolbox loaded?" If yes, `IN_COLAB` becomes `True`; if not, `False`. It's like checking whether you're at school or at home, so you know which rules to follow.

```python
warnings.filterwarnings("ignore", category=FutureWarning)
warnings.filterwarnings("ignore", message="Consider using IPython.display.IFrame")
```
- "Please don't show these two kinds of harmless warning messages." They aren't errors, just noise that would clutter the screen.

```python
pd.set_option("display.width", 160)
pd.set_option("display.max_columns", 20)
```
- Tell pandas: "When you show a table, make it up to 160 characters wide and show up to 20 columns," so tables don't get cut off.

```python
SUBJECTS = ["Python", "SQL", "Power BI", "AI"]
SCORE_COLS = SUBJECTS + ["Attendance"]
```
- `SUBJECTS`: a list of the 4 subject names.
- `SCORE_COLS`: the 4 subjects **plus** Attendance (adding two lists glues them together). These are all the number columns.
- The names are in CAPITAL LETTERS because they're **constants**: values we set once and never change.

```python
CSV_PATH = "student_performance.csv"
PROCESSED_PATH = "student_performance_processed.csv"
DASHBOARD_PATH = "student_dashboard.html"
CHART_DIR = "charts"
```
- Labelled boxes holding **file names**: the raw data file, the cleaned file we'll save, the dashboard file, and the folder for chart pictures. If we ever want a different name, we change it in just one place.

```python
HIGH_SCORE = 85
LOW_SCORE = 60
NEEDS_HELP_AVG = 75
NEEDS_HELP_SUBJECT = 70
```
- Our **rules**, written as numbers:
  - Above 85 counts as a "high scorer".
  - Below 60 counts as a "low scorer".
  - An average below 75 means "needs help".
  - Any single subject below 70 also means "needs help".

```python
GRADE_BANDS = [(90, "A+"), (80, "A"), (70, "B"), (60, "C"), (50, "D"), (0, "F")]
GRADE_ORDER = [g for _, g in GRADE_BANDS]
```
- `GRADE_BANDS`: the grading ladder. 90 or more is A+, 80 or more is A, and so on down to F.
- `GRADE_ORDER`: picks out **only the letters** → `["A+", "A", "B", "C", "D", "F"]`.
  - `for _, g in GRADE_BANDS` means "for each pair, ignore the number (`_` means 'I don't care about this one') and keep the letter `g`."

```python
SUBJECT_COLORS = {"Python": "#4F46E5", "SQL": "#0EA5A4", "Power BI": "#F59E0B", "AI": "#E11D48"}
GRADE_COLORS = {"A+": "#4F46E5", "A": "#0EA5A4", "B": "#F59E0B", "C": "#F97316", "D": "#E11D48", "F": "#991B1B"}
```
- A **colour for each subject** and **each grade**, so Python is always purple, SQL always teal, and so on in every chart.
- `#4F46E5` is a colour code: the computer's way of naming an exact colour.

```python
os.makedirs(CHART_DIR, exist_ok=True)
```
- Make a folder called `charts`. `exist_ok=True` means "if it's already there, that's fine, don't complain."

```python
sns.set_theme(style="whitegrid", font_scale=0.95)
plt.rcParams.update({"figure.dpi": 110, "axes.titleweight": "bold", "axes.titlesize": 13,
                     "axes.spines.top": False, "axes.spines.right": False})
```
- `sns.set_theme`: give all charts a **white background with light grid lines** and slightly smaller text.
- `plt.rcParams.update`: the **default style** for every matplotlib chart:
  - `dpi 110`: how sharp the picture is.
  - Bold titles, size 13.
  - `spines.top / right False`: remove the box lines on the top and right of the chart so it looks cleaner.

```python
pio.templates.default = "plotly_white"
if IN_COLAB:
    pio.renderers.default = "colab"
```
- Plotly charts use a clean white style.
- **If** we're in Colab, tell plotly to show charts the Colab way, so they appear properly there.

```python
print("Running in Colab:", IN_COLAB)
print("pandas", pd.__version__, "| plotly", plotly.__version__, "| altair", alt.__version__)
```
- Show a message saying whether we're in Colab, and which **version** of each toolbox we have (like checking which edition of a book you're reading).

---

## Section 2.1 – Making the data file

```python
RAW_CSV = """Student,Python,SQL,Power BI,AI,Attendance
Alice,85,78,92,88,95
...
Zara,82,85,84,86,0
"""
```
- A big string holding the **whole table** from the assignment. The three quotes `"""` let text stretch over many lines.
- **CSV** means "Comma-Separated Values": each line is one student, and commas split the columns.

```python
if os.path.exists(CSV_PATH):
    print(f"'{CSV_PATH}' already exists, using the existing file.")
```
- "Does the file already exist?" If **yes**, just say so and don't overwrite it. (Maybe you uploaded your own copy.)

```python
else:
    with open(CSV_PATH, "w", encoding="utf-8") as f:
        f.write(RAW_CSV)
    print(f"Created '{CSV_PATH}'.")
```
- **Otherwise** (the file isn't there):
  - `open(..., "w")`: open a new file for **writing** (`"w"`). `encoding="utf-8"` is the alphabet the computer uses to store the letters.
  - `with ... as f`: "while the file is open, call it `f`, and **close it automatically** when we're done," like a door that shuts by itself.
  - `f.write(RAW_CSV)`: write our table into the file.
  - `print(...)`: tell the user the file was created.

---

## Section 2.2 – Reading the data

```python
raw_df = pd.read_csv(CSV_PATH, na_values=["Null", "null", "NULL", "NA", "N/A", ""])
```
- Read the CSV file into a pandas **table** called `raw_df` ("raw" means untouched, like raw vegetables).
- `na_values=[...]`: "If you see the word `Null` (or null, NA, or an empty cell), treat it as **empty** (`NaN`)." Otherwise Python would think "Null" is a word, not a missing mark.

```python
raw_df["Student"] = raw_df["Student"].str.strip()
```
- Take the Student column and **trim extra spaces** off each name, so `" Alice "` becomes `"Alice"`. `.str` means "do a text operation".

```python
print(f"Loaded {raw_df.shape[0]} rows x {raw_df.shape[1]} columns")
display(raw_df)
```
- `raw_df.shape` is the table's **size**: (rows, columns). `[0]` is rows (25 students), `[1]` is columns (6).
- `display(raw_df)` shows the whole table.

---

## Section 2.3 – Information about the dataset

```python
raw_df.info()
```
- Prints a **summary card** of the table: column names, how many cells are filled in, and the type of each column (numbers or text).

```python
print()
```
- Prints an empty line, just to add space.

```python
display(pd.DataFrame({
    "dtype": raw_df.dtypes.astype(str),
    "non-null": raw_df.notna().sum(),
    "nulls": raw_df.isna().sum(),
    "zeros": (raw_df == 0).sum(),
    "unique": raw_df.nunique(),
}))
```
- Builds a **new little table** describing every column:
  - `dtype`: what type of data it is (`int` = whole numbers, `float` = decimal numbers, `str` = text).
  - `non-null`: how many cells are **filled**. `notna()` marks each filled cell True, and `.sum()` counts the Trues (True counts as 1).
  - `nulls`: how many cells are **empty** (`isna()`).
  - `zeros`: how many cells are exactly **0**.
  - `unique`: how many **different** values the column has.

---

## Section 3 – Data Cleaning Module

### 3.1 Finding missing values

```python
missing_summary = pd.DataFrame({
    "Missing": raw_df.isna().sum(),
    "% Missing": (raw_df.isna().mean() * 100).round(1),
})
display(missing_summary)
```
- Counts the missing cells in each column, plus the **percentage**.
  - `.mean()` of True/False values gives the fraction that's True (for example 0.04).
  - `* 100` turns that into a percentage, and `.round(1)` keeps 1 decimal place.

```python
print(f"Total missing cells: {int(raw_df.isna().sum().sum())}")
```
- `.sum().sum()`: the first sum counts each column, and the second adds the columns together, giving **5** missing cells in total. `int()` makes it a plain whole number.

```python
print("\nRows that contain missing values:")
display(raw_df[raw_df.isna().any(axis=1)])
```
- `\n` means "new line".
- `raw_df.isna().any(axis=1)` asks, for **each row** (`axis=1` means go across the row): "does this student have **any** empty cell?"
- `raw_df[ ... ]` keeps **only those students**. That's called **filtering**, like a sieve.

```python
fig, ax = plt.subplots(figsize=(8, 5.5))
```
- Make a blank **drawing paper** (`fig`) and a **drawing area** on it (`ax`), 8 by 5.5 inches.

```python
sns.heatmap(raw_df.set_index("Student")[SCORE_COLS].isna(), cbar=False, cmap=["#EEF2FF", "#E11D48"],
            linewidths=0.5, linecolor="white", ax=ax)
```
- Draw a **heatmap**, a grid of coloured squares:
  - `set_index("Student")`: use student names as row labels.
  - `[SCORE_COLS].isna()`: True where a value is missing.
  - `cmap=[light, red]`: False → light colour, True → **red**, so missing cells glow red.
  - `cbar=False`: no colour bar on the side.
  - `linewidths`: thin white lines between the squares.

```python
ax.set_title("Missing values map (red = missing)")
ax.set_xlabel("Column"); ax.set_ylabel("Student")
```
- Give the chart a **title** and **labels** for the bottom (x) and side (y). `;` lets us put two commands on one line.

```python
plt.tight_layout(); plt.savefig(f"{CHART_DIR}/00_missing_values_map.png", bbox_inches="tight"); plt.show()
```
- `tight_layout()`: tidy up the spacing so nothing overlaps.
- `savefig(...)`: **save the picture** as a PNG file in the charts folder.
- `show()`: show it on screen.

### 3.2 Finding zeros

```python
zero_mask = raw_df[SCORE_COLS].eq(0)
```
- `.eq(0)` means "is equal to 0?" for every cell. This gives a True/False table called a **mask** (like a stencil).

```python
print("Zero values per column:")
display(zero_mask.sum().to_frame("Zeros"))
```
- Count the zeros in each column, and turn the answer into a small table with the heading "Zeros".

```python
print("Rows containing zero values:")
display(raw_df[zero_mask.any(axis=1)])
```
- Show only the students who have **at least one 0**: Michael, Peter, William and Zara.

### 3.3 Duplicates

```python
exact_dupes = raw_df.duplicated()
```
- For each row: "Is this row an **exact copy** of an earlier row?" True or False.

```python
name_dupes = raw_df["Student"].str.lower().duplicated(keep=False)
```
- Make every name lowercase (so "Alice" and "alice" count as the same), then check whether a name appears **more than once**. `keep=False` marks **all** the copies, not just the second one.

```python
print(f"Exact duplicate rows : {int(exact_dupes.sum())}")
print(f"Repeated names       : {int(name_dupes.sum())}")
```
- Count them and print the result. (Both are 0, so our data has no duplicates.)

```python
if name_dupes.any():
    display(raw_df[name_dupes])
```
- **Only if** there is a repeated name, show those rows.

```python
dedup_df = (raw_df.drop_duplicates()
                  .drop_duplicates(subset="Student", keep="first")
                  .reset_index(drop=True))
```
- Make a new table `dedup_df` ("de-duplicated"):
  1. `drop_duplicates()`: remove exact copy rows.
  2. `drop_duplicates(subset="Student", keep="first")`: if a name appears twice, keep only the **first** one.
  3. `reset_index(drop=True)`: renumber the rows 0, 1, 2… neatly again.
- The brackets `( )` just let us split one long command across several lines.

```python
print(f"Rows before: {len(raw_df)} -> rows after duplicate removal: {len(dedup_df)}")
```
- `len()` means "how many rows?" This prints 25 → 25.

### 3.4 Validation (checking the rules)

```python
def validate(df):
    """Run the data-quality checks and return a pass/fail table."""
```
- `def` starts a **recipe** called `validate`. It takes one ingredient, a table called `df`.
- The text in `""" """` is a **docstring**: a note explaining what the recipe does.

```python
    values = df[SCORE_COLS].stack()
```
- `.stack()` **pours** the whole number grid into one long line of values (and skips empty ones), so we can check them all at once.

```python
    checks = {
        "Every row has a student name": df["Student"].notna().all(),
        "Student names are unique": df["Student"].is_unique,
        "Score columns are numeric": all(pd.api.types.is_numeric_dtype(df[c]) for c in SCORE_COLS),
        "All scores within 0-100": values.between(0, 100).all(),
        "No missing values": not df[SCORE_COLS].isna().any().any(),
        "No zero (invalid) values": not df[SCORE_COLS].eq(0).any().any(),
    }
```
- A dictionary of **6 questions**. Each one becomes True (pass) or False (fail):
  1. Does **every** student have a name? (`.all()` means "are all of them True?")
  2. Is every name **different**? (`is_unique`)
  3. Is every score column made of **numbers**? `all(... for c in SCORE_COLS)` checks each column `c` one by one.
  4. Is every value **between 0 and 100**?
  5. Are there **no** empty cells? `.any().any()` asks "is there any empty cell anywhere?", and `not` flips the answer.
  6. Are there **no** zeros?

```python
    return pd.DataFrame({"Check": list(checks), "Passed": [bool(v) for v in checks.values()]})
```
- Hand back a neat **table** with two columns: the question, and whether it passed.
  - `list(checks)` gives the questions (the dictionary's keys).
  - `[bool(v) for v in checks.values()]` turns each answer into a plain True or False.

```python
def show_checks(table):
    return table.style.map(lambda v: "color:#059669;font-weight:600" if v is True
                           else "color:#DC2626;font-weight:600" if v is False else "", subset=["Passed"])
```
- A recipe that **colours** the pass/fail table: True in **green**, False in **red**.
  - `.style.map(...)` applies a style to every cell in the "Passed" column.
  - `lambda v: ...` is a **tiny one-line recipe** with no name: "given a value v, pick a colour."

```python
print("Validation of the RAW data:")
display(show_checks(validate(dedup_df)))
```
- Run the checks on the uncleaned data and show the coloured result. 3 checks **fail** (range, missing, zeros).

### 3.5 Outliers (values that are weirdly far from the others)

```python
def iqr_outliers(df, cols):
    """Return one row per value that falls outside the 1.5 x IQR fences."""
    rows = []
```
- A recipe that finds **outliers**. `rows = []` starts with an empty basket for the results.

```python
    for col in cols:
        s = df[col].dropna()
```
- **For each column**: take its values and throw away the empty ones (`dropna`).

```python
        q1, q3 = s.quantile([0.25, 0.75])
```
- Line the marks up from smallest to biggest:
  - **Q1** is the mark ¼ of the way along.
  - **Q3** is the mark ¾ of the way along.

```python
        iqr = q3 - q1
        lo, hi = q1 - 1.5 * iqr, q3 + 1.5 * iqr
```
- **IQR** is the gap between Q1 and Q3, where the "middle half" of the class sits.
- We build two **fences**: a low fence and a high fence. Anything outside them is suspicious, like a 2-metre-tall kid in a 5th grade class photo.

```python
        for _, r in df[(df[col] < lo) | (df[col] > hi)].iterrows():
```
- Find the students whose value is **below the low fence OR (`|`) above the high fence**, then go through them one at a time (`iterrows` = go row by row).

```python
            rows.append({"Student": r["Student"], "Column": col, "Value": r[col],
                         "Lower fence": round(lo, 1), "Upper fence": round(hi, 1)})
```
- Put a note in the basket: who it is, which column, the value, and where the fences were.

```python
    return pd.DataFrame(rows, columns=["Student", "Column", "Value", "Lower fence", "Upper fence"])
```
- Turn the basket into a table and hand it back.

```python
raw_outliers = iqr_outliers(dedup_df, SCORE_COLS)
print(f"Outliers found in raw data: {len(raw_outliers)}")
display(raw_outliers)
```
- Run it on the raw data. It finds **4** outliers, and all 4 are the zeros.

### 3.6 Fixing the problems

```python
def build_issue_log(df):
    """List every missing, zero or out-of-range score in the raw data."""
    rows = []
    for _, r in df.iterrows():
        for col in SCORE_COLS:
            v = r[col]
```
- A recipe that writes a **diary of every problem**.
- Two loops, one inside the other: **for every student**, then **for every score column**. `v` is that one cell.

```python
            if pd.isna(v):
                issue = "Missing (Null)"
            elif v == 0:
                issue = "Zero value"
            elif not 0 <= v <= 100:
                issue = "Out of range"
            else:
                continue
```
- Decide what's wrong with the cell:
  - Empty? → "Missing".
  - Exactly 0? → "Zero value". (`elif` means "else if".)
  - Not between 0 and 100? → "Out of range".
  - Otherwise it's fine → `continue`, which means "skip to the next cell, nothing to write."

```python
            rows.append({"Student": r["Student"], "Column": col,
                         "Original": "Null" if pd.isna(v) else int(v), "Issue": issue})
    return pd.DataFrame(rows, columns=["Student", "Column", "Original", "Issue"])
```
- Write the problem into the diary: who, which column, the original value ("Null" if empty, otherwise the number), and what the issue was. Then return the diary as a table.

```python
def clean_scores(df):
    """Return a cleaned copy: invalid values -> NaN -> imputed, scores stored as int."""
    out = df.copy()
```
- The **washing machine** recipe. `df.copy()` makes a **photocopy**, so the original table stays untouched.

```python
    scores = out[SCORE_COLS].mask(out[SCORE_COLS].eq(0))
```
- `.mask(...)` means "**cover up** these cells and make them empty." Here, every 0 becomes empty, because a 0 is a mistake and not a real mark.

```python
    scores = scores.where((scores >= 0) & (scores <= 100))
```
- `.where(...)` means "**keep** cells where this is True, and empty the rest." So anything below 0 or above 100 is also emptied. (`&` means AND.)

```python
    student_mean = scores[SUBJECTS].mean(axis=1)
```
- For **each student** (`axis=1` means across the row), work out the average of the subjects they **do** have. Empty cells are skipped automatically.
- Example: Michael has 82, 80, 78 → mean = **80**.

```python
    for subject in SUBJECTS:
        scores[subject] = scores[subject].fillna(student_mean)
```
- For each subject, **fill each empty cell** (`fillna`) with that student's own average. Michael's missing Python mark becomes 80.

```python
    scores["Attendance"] = scores["Attendance"].fillna(scores["Attendance"].median())
```
- Fill missing attendance with the class **median**: line up everyone's attendance and pick the **middle** one (92). Sophia and Zara get 92.

```python
    out[SCORE_COLS] = scores.round(0).astype(int)
    return out
```
- Round to whole numbers, turn them into integers (`int`), put the clean numbers back in the photocopy, and return it.

```python
issue_log = build_issue_log(dedup_df)
clean_df = clean_scores(dedup_df)
```
- Actually **cook** both recipes: make the problem diary, and make the clean table.

```python
issue_log["Imputed value"] = [int(clean_df.loc[clean_df["Student"] == s, c].iat[0])
                              for s, c in zip(issue_log["Student"], issue_log["Column"])]
```
- Add a column to the diary showing the **new value** for each problem:
  - `zip(...)` pairs each student `s` with their column `c`, like zipping up a jacket.
  - `clean_df.loc[row condition, column]` finds that cell in the clean table.
  - `.iat[0]` takes the first (and only) answer.

```python
issue_log["Method"] = np.where(issue_log["Column"] == "Attendance",
                               "Class median attendance", "Student's mean of valid subjects")
```
- `np.where(question, if yes, if no)`: if the column is Attendance, write "Class median"; otherwise write "Student's mean".

```python
print(f"{len(issue_log)} values were corrected:")
display(issue_log)
```
- Shows "9 values were corrected" and the diary table.

```python
affected = issue_log["Student"].unique()
```
- The list of students who had a problem, each listed only once (`unique`).

```python
before_after = dedup_df.set_index("Student").loc[affected, SCORE_COLS].merge(
    clean_df.set_index("Student").loc[affected, SCORE_COLS],
    left_index=True, right_index=True, suffixes=(" (raw)", " (clean)"))
```
- Put the **before** and **after** tables side by side for those students:
  - `.merge(...)` joins two tables. `left_index/right_index=True` means "match the rows by student name."
  - `suffixes` adds " (raw)" and " (clean)" to the column names so we can tell them apart.

```python
before_after = before_after[[f"{c} ({k})" for c in SCORE_COLS for k in ("raw", "clean")]]
```
- **Reorder** the columns so each raw/clean pair sits together: `Python (raw)`, `Python (clean)`, `SQL (raw)`…

```python
print("Before vs after for the affected students:")
display(before_after)
print("\nValidation of the CLEANED data:")
validation_clean = validate(clean_df)
display(show_checks(validation_clean))
```
- Show the before/after table, then run the 6 checks again on the **clean** data. All of them are green now ✅.

```python
fig, axes = plt.subplots(1, 2, figsize=(13, 4.5), sharey=True)
```
- Make paper with **2 drawing areas side by side** (1 row, 2 columns). `sharey=True` means both use the same up-and-down scale.

```python
for ax, (title, frame) in zip(axes, [("Raw data (zeros pull boxes down)", dedup_df), ("Cleaned data", clean_df)]):
```
- Loop twice: the first time draw the raw data in the left area, the second time the clean data in the right area.

```python
    long = frame.melt(id_vars="Student", value_vars=SCORE_COLS, var_name="Column", value_name="Score")
```
- `.melt()` **reshapes** the table from wide to long. Instead of one row per student with 5 columns, we get one row per (student, column, score). Seaborn likes data in this shape.

```python
    sns.boxplot(data=long, x="Column", y="Score", hue="Column", palette="Set2", legend=False, ax=ax)
    ax.set_title(title); ax.set_xlabel(""); ax.set_ylabel("Score")
```
- Draw a **box plot** for each column. A box plot shows where most values sit (the box), and dots for the outliers.

```python
plt.suptitle("Outlier check: before vs after cleaning", fontweight="bold")
plt.tight_layout(); plt.savefig(f"{CHART_DIR}/01_outliers_before_after.png", bbox_inches="tight"); plt.show()
```
- A big title over both pictures, then tidy, save and show.

```python
clean_outliers = iqr_outliers(clean_df, SCORE_COLS)
print(f"Outliers remaining after cleaning: {len(clean_outliers)}")
display(clean_outliers)
print("These are real (if unusual) results, so they stay in the data and are only flagged.")
```
- Check for outliers again after cleaning. There are **0** left.

---

## Section 4 – New calculated columns

```python
def assign_grade(avg):
    return next(grade for cutoff, grade in GRADE_BANDS if avg >= cutoff)
```
- A recipe that turns an average into a **letter grade**.
- It walks down the ladder (90 → A+, 80 → A, …) and `next(...)` grabs the **first** step where the average is big enough. For 82.6, 90 is too high, 80 fits, so the grade is **A**.

```python
def add_features(df, issues):
    out = df.copy()
    out["Total"] = out[SUBJECTS].sum(axis=1)
```
- Photocopy the table, then add **Total**: the 4 marks added up across each row.

```python
    out["Average"] = out[SUBJECTS].mean(axis=1).round(2)
```
- **Average** is the total ÷ 4, rounded to 2 decimal places.

```python
    out["Grade"] = out["Average"].apply(assign_grade)
```
- `.apply(...)` runs the grade recipe on **every** student's average.

```python
    out["Rank"] = out["Average"].rank(ascending=False, method="min").astype(int)
```
- **Rank**: the biggest average gets 1.
  - `ascending=False` means biggest first.
  - `method="min"` means if two students tie, they **share** the better rank. (Laura and Ryan both get 13.)

```python
    out["Best Subject"] = out[SUBJECTS].idxmax(axis=1)
    out["Weakest Subject"] = out[SUBJECTS].idxmin(axis=1)
```
- `idxmax` gives the **name** of the column with the biggest mark, and `idxmin` the smallest.

```python
    needs_help = (out["Average"] < NEEDS_HELP_AVG) | (out[SUBJECTS].min(axis=1) < NEEDS_HELP_SUBJECT)
    out["Status"] = np.where(needs_help, "Needs Improvement", "On Track")
```
- A student needs help **if** their average is under 75 **OR** their lowest subject is under 70. Label them "Needs Improvement", otherwise "On Track".

```python
    imputed = issues.groupby("Student")["Column"].apply(", ".join)
    out["Imputed"] = out["Student"].map(imputed).fillna("")
    return out
```
- `groupby("Student")`: gather each student's problems into one pile, and join the column names with commas.
- `.map(...)`: look up each student in that list. Students with no problems get blank text (`fillna("")`).

```python
df = add_features(clean_df, issue_log)
ranked = df.sort_values(["Rank", "Student"]).reset_index(drop=True)
display(ranked)
```
- `df` is now our **final, complete table**.
- `ranked` is the same table **sorted** by rank, from best to worst (ties sorted by name).

---

## Section 5 – Descriptive statistics

```python
stats = df[SCORE_COLS + ["Total", "Average"]].describe().T
```
- `.describe()` instantly calculates the **count, mean, standard deviation, min, 25%, 50%, 75% and max** for each column.
- `.T` (**transpose**) flips the table on its side so it's easier to read.

```python
stats["median"] = df[SCORE_COLS + ["Total", "Average"]].median()
stats["skew"] = df[SCORE_COLS + ["Total", "Average"]].skew()
display(stats.round(2))
```
- Add the **median** (the middle value) and the **skew**, which tells us whether the marks lean more towards the low side or the high side.

```python
print("Grade distribution:")
display(df["Grade"].value_counts().reindex(GRADE_ORDER, fill_value=0).to_frame("Students").T)
```
- `value_counts()`: **count** how many students got each grade.
- `reindex(GRADE_ORDER, fill_value=0)`: put the grades in order A+, A, B… and show 0 for grades nobody got.

---

## Section 6 – KPI cards (the big number boxes)

```python
top_row = ranked.iloc[0]
low_row = ranked.iloc[-1]
```
- `iloc[0]` is the **first** row (the topper, Queen). `iloc[-1]` is the **last** row (David). `-1` means "count from the end".

```python
kpis = {
    "total_students": int(len(df)),
    "avg_attendance": round(float(df["Attendance"].mean()), 2),
    "highest_avg": float(top_row["Average"]),
    "highest_name": str(top_row["Student"]),
    "lowest_avg": float(low_row["Average"]),
    "lowest_name": str(low_row["Student"]),
    "class_avg": round(float(df["Average"].mean()), 2),
    "above_85": int((df["Average"] > HIGH_SCORE).sum()),
    "below_60": int((df["Average"] < LOW_SCORE).sum()),
}
```
- A dictionary holding all **7 headline numbers** the assignment asked for.
- `(df["Average"] > 85).sum()` counts the Trues, which is how many students are above 85.
- `int()`, `float()` and `str()` convert values into plain Python whole numbers, decimal numbers and text.

```python
KPI_CARDS = [
    ("👥", "Total Students", kpis["total_students"], "enrolled", "#4F46E5"),
    ...
]
```
- A list of cards. Each card is a group of 5 things: **icon, label, value, small text, colour**.

```python
def kpi_html(cards):
    items = "".join(
        f"""<div style="...border-top:4px solid {color};...">
              <div ...>{icon} {label}</div>
              <div ...>{value}</div>
              <div ...>{sub}</div></div>"""
        for icon, label, value, sub, color in cards)
    return f'<div style="display:flex;flex-wrap:wrap;gap:12px;...">{items}</div>'
```
- A recipe that writes **web-page code (HTML)** for the cards:
  - For each card, fill the holes `{icon}`, `{label}` and so on in a little HTML box.
  - `"".join(...)` glues all the boxes together into one long string.
  - The outer `<div style="display:flex;flex-wrap:wrap">` lines the boxes up in a row and wraps them onto the next line if there isn't enough room.

```python
display(HTML(kpi_html(KPI_CARDS)))
```
- Show the cards in the notebook as real coloured boxes.

---

## Section 7 – Student analysis

### 7.1 Rankings table with highlights

```python
def highlight_extremes(row):
    if row["Rank"] == ranked["Rank"].min():
        return ["background-color:#FEF3C7;font-weight:600"] * len(row)
    if row["Average"] == ranked["Average"].min():
        return ["background-color:#FEE2E2"] * len(row)
    return [""] * len(row)
```
- A recipe that decides the **background colour** of each row:
  - Rank 1 (the topper) → **gold** and bold.
  - Lowest average → **light red**.
  - Everyone else → no colour.
- `[...] * len(row)` repeats the style once for **every cell** in the row.

```python
rank_cols = ["Rank", "Student", *SUBJECTS, "Total", "Average", "Grade", "Attendance", "Status"]
```
- The columns we want to show. `*SUBJECTS` **unpacks** the subjects list right here, as if we'd typed all 4 names.

```python
display(ranked[rank_cols].style
        .apply(highlight_extremes, axis=1)
        .background_gradient(subset=["Average"], cmap="Blues")
        .bar(subset=["Attendance"], color="#A7F3D0", vmin=80, vmax=100)
        .format({"Average": "{:.2f}"})
        .hide(axis="index"))
```
- Decorate the table step by step:
  - `.apply(highlight_extremes, axis=1)`: gold and red rows.
  - `.background_gradient`: the Average column gets darker blue for bigger numbers.
  - `.bar`: a small green **bar** inside each Attendance cell (80% means empty, 100% means full).
  - `.format`: show averages with 2 decimal places.
  - `.hide(axis="index")`: hide the 0, 1, 2… row numbers.

### 7.2 Topper and lowest cards

```python
def profile_card(row, title, color):
    marks = " · ".join(f"{s}: <b>{row[s]}</b>" for s in SUBJECTS)
    return f"""<div ...>... {row['Student']} ... Average {row['Average']:.2f} ... {marks} ...</div>"""
```
- A recipe that builds a pretty **name card** for one student.
- `marks` makes text like "Python: **94** · SQL: **96** …". `<b>` means bold.
- `{row['Average']:.2f}` shows the number with 2 decimals.

```python
display(HTML('<div style="display:flex;gap:14px;flex-wrap:wrap">'
             + profile_card(top_row, "🏆 CLASS TOPPER", "#D97706")
             + profile_card(low_row, "📉 NEEDS THE MOST SUPPORT", "#DC2626") + "</div>"))
```
- Show **two cards side by side**: Queen (gold) and David (red). `+` glues strings together.

### 7.3 One student's full report

```python
def student_report(name):
    row = df.loc[df["Student"].str.lower() == name.strip().lower()]
```
- Find the student whose name matches. Both names are made lowercase, so "michael" still finds "Michael".

```python
    if row.empty:
        raise ValueError(f"No student called '{name}'. Options: {', '.join(sorted(df['Student']))}")
```
- If nobody matched, **stop with a helpful error** listing the real names. `raise` means "shout an error".

```python
    r = row.iloc[0]
    detail = pd.DataFrame({
        "Student": [r[c] for c in SCORE_COLS],
        "Class average": [round(df[c].mean(), 2) for c in SCORE_COLS],
    }, index=SCORE_COLS)
    detail["Difference"] = (detail["Student"] - detail["Class average"]).round(2)
```
- Build a table: the student's score, the class average, and the **difference** for every column. A positive difference means better than average.

```python
    display(HTML(profile_card(r, "STUDENT PROFILE", "#4F46E5")))
    display(detail)
    if r["Imputed"]:
        print(f"Note: imputed during cleaning -> {r['Imputed']}")

student_report("Michael")
```
- Show the card and the table. If this student had a mark fixed during cleaning, say so.
- The last line **uses** the recipe on Michael as an example.

### 7.4 Compare students

```python
def compare_students(names):
    cols = SCORE_COLS + ["Total", "Average"]
    table = df.set_index("Student").loc[names, cols].T
    table["Class avg"] = df[cols].mean().round(2)
    return table

display(compare_students(["Queen", "Xavier", "Grace", "David"]))
```
- Pick the students by name, take their score columns, and **flip** the table (`.T`) so each student becomes a column. Then add a "Class avg" column for comparison.

### 7.5 Filters

```python
def filter_students(min_avg=0, max_avg=100, min_attendance=0, subject=None, min_subject=0, grade=None, search=""):
```
- A recipe with **settings that have default values**. If you don't give a setting, the default is used: `min_avg=0` means "no minimum", `subject=None` means "no subject filter".

```python
    mask = df["Average"].between(min_avg, max_avg) & (df["Attendance"] >= min_attendance)
```
- Start the sieve: keep students whose average is in range **AND** whose attendance is high enough.

```python
    if subject:
        mask &= df[subject] >= min_subject
    if grade:
        mask &= df["Grade"] == grade
    if search:
        mask &= df["Student"].str.contains(search, case=False, regex=False)
```
- Add more holes to the sieve, but **only if** that setting was given:
  - `&=` means "and also must…".
  - `str.contains(search, case=False)`: the name contains the typed letters, ignoring capital letters. So typing "ra" finds "Grace" and "Zara".

```python
    return df.loc[mask, rank_cols].sort_values("Rank")
```
- Return only the matching students, sorted by rank.

```python
print("Average >= 85 and attendance >= 95%:")
display(filter_students(min_avg=85, min_attendance=95))
print("Scored at least 90 in AI:")
display(filter_students(subject="AI", min_subject=90))
```
- Two examples of using the filter.

---

## Section 8 – Charts 🎨

Most chart cells follow the **same 5 steps**, so once you understand one, you understand them all:
1. Make paper (`plt.subplots`, or `go.Figure()` for plotly).
2. Draw something (bars, lines, dots…).
3. Add a title and axis labels.
4. Save it to a file.
5. Show it.

### 8.1 Bar chart

```python
subject_avg = df[SUBJECTS].mean().round(2)
```
- The average mark for each subject.

```python
fig, ax = plt.subplots(figsize=(8, 4.5))
bars = ax.bar(SUBJECTS, subject_avg, color=[SUBJECT_COLORS[s] for s in SUBJECTS], width=0.6)
```
- Draw 4 bars, one per subject, each in its own colour. `width=0.6` makes the bars a bit thin so there are gaps between them.

```python
ax.bar_label(bars, fmt="%.2f", padding=3, fontweight="bold")
```
- Write the number **on top of each bar** (2 decimals, in bold).

```python
ax.axhline(subject_avg.mean(), ls="--", color="#6B7280", lw=1, label=f"Overall mean {subject_avg.mean():.2f}")
```
- Draw a **dashed horizontal line** (`axhline`) at the overall average. `ls="--"` makes it dashed and `lw` is the line width.

```python
ax.set_ylim(0, 100); ax.set_title(...); ax.set_xlabel("Subject"); ax.set_ylabel("Average marks")
ax.legend(loc="lower right")
plt.tight_layout(); plt.savefig(...); plt.show()
```
- The y-axis goes from 0 to 100. Then a title, labels and a **legend** (the little key explaining the dashed line), followed by save and show.

### 8.2 Histogram

```python
sns.histplot(df["Average"], bins=range(60, 101, 5), kde=True, color="#4F46E5", edgecolor="white", ax=ax)
```
- A **histogram** sorts students into **buckets** (60–65, 65–70, … 95–100) and draws a bar showing how many students fall into each bucket.
- `range(60, 101, 5)` makes bucket edges every 5 marks.
- `kde=True` adds a smooth curved line over the bars.

```python
ax.axvline(df["Average"].mean(), color="#E11D48", ls="--", label=...)
```
- A **vertical** red dashed line (`axvline`) at the class average.

### 8.3 Line chart

```python
for s in SUBJECTS:
    ax.plot(ranked["Student"], ranked[s], marker="o", ms=4, lw=1.8, label=s, color=SUBJECT_COLORS[s])
```
- For each subject, draw a **line** joining all the students' marks, going from rank 1 to rank 25. `marker="o"` puts a small dot on each point.

```python
ax.plot(ranked["Student"], ranked["Average"], color="#111827", lw=2.5, ls="--", label="Average")
```
- A thicker black dashed line for each student's average.

```python
ax.tick_params(axis="x", rotation=60); ax.legend(ncol=5, loc="lower left")
```
- **Tilt** the names on the bottom by 60° so they don't crash into each other, and spread the legend across 5 columns.

### 8.4 Pie (donut) chart

```python
grade_counts = df["Grade"].value_counts().reindex(GRADE_ORDER).dropna().astype(int)
```
- Count the students in each grade, in order, and drop any grade nobody got.

```python
ax.pie(grade_counts, labels=[f"Grade {g}" for g in grade_counts.index],
       autopct=lambda p: f"{p:.0f}%\n({round(p * len(df) / 100)})",
       colors=[GRADE_COLORS[g] for g in grade_counts.index], startangle=90, counterclock=False,
       wedgeprops={"width": 0.45, "edgecolor": "white"}, textprops={"fontsize": 11})
```
- Draw the pie:
  - `autopct`: a tiny recipe that writes "**40%** (10)" in each slice, giving the percentage and the number of students.
  - `startangle=90`: start at the top, like 12 o'clock.
  - `counterclock=False`: go clockwise.
  - `wedgeprops width 0.45`: cut out the middle so it becomes a **donut** 🍩.

### 8.5 Box plot

```python
long_df = df.melt(id_vars=["Student", "Grade"], value_vars=SUBJECTS, var_name="Subject", value_name="Marks")
sns.boxplot(data=long_df, x="Subject", y="Marks", hue="Subject", palette=SUBJECT_COLORS, width=0.5, legend=False, ax=ax)
sns.stripplot(data=long_df, x="Subject", y="Marks", color="#111827", size=4, alpha=0.5, jitter=0.15, ax=ax)
```
- Reshape the table (melt), draw a box for each subject, then sprinkle **every student as a dot** on top (`stripplot`).
  - `jitter` wiggles the dots sideways a little so they don't hide behind each other.
  - `alpha=0.5` makes them half see-through.

### 8.6 Heatmaps

```python
fig, axes = plt.subplots(1, 2, figsize=(15, 7), gridspec_kw={"width_ratios": [1, 1.1]})
```
- Two drawing areas side by side. The right one is slightly wider.

```python
corr = df[SCORE_COLS + ["Average"]].corr()
sns.heatmap(corr, annot=True, fmt=".2f", cmap="RdBu_r", vmin=-1, vmax=1, square=True, linewidths=0.5, ax=axes[0])
```
- `.corr()` makes a **correlation** table, which answers "when one goes up, does the other go up too?"
  - 1 means they **always** move together.
  - 0 means no connection.
  - −1 means they move in opposite directions.
- `annot=True` writes the number inside each square. `RdBu_r` colours: red for strong positive, blue for negative.

```python
sns.heatmap(ranked.set_index("Student")[SUBJECTS], annot=True, fmt="d", cmap="YlGnBu", ...)
```
- The second heatmap shows every student's marks as colours: darker means higher. `fmt="d"` means whole numbers.

### 8.7 Attendance vs marks

```python
att_corr = df["Attendance"].corr(df["Average"])
```
- One number saying how strongly attendance and marks are connected. It's **0.93**, which is very strong!

```python
sns.regplot(data=df, x="Attendance", y="Average", ax=ax, color="#4F46E5",
            scatter_kws={"s": 60, "edgecolor": "white"}, line_kws={"color": "#E11D48"})
```
- A **scatter plot** (one dot per student) plus a red **best-fit line** that shows the trend.

```python
for _, r in df.iterrows():
    ax.annotate(r["Student"], (r["Attendance"], r["Average"]), textcoords="offset points", xytext=(4, 4), fontsize=7.5, color="#374151")
```
- For every student, write their **name next to their dot**, nudged 4 points up and to the right.

### 8.8 Bubble chart (Plotly, interactive)

```python
fig = px.scatter(df, x="Attendance", y="Average", size="Total", color="Grade", text="Student",
                 color_discrete_map=GRADE_COLORS, category_orders={"Grade": GRADE_ORDER}, size_max=28,
                 hover_data={...}, title="...")
```
- A scatter plot where **bubble size = total marks** and **colour = grade**. Names are written on the bubbles.
- `hover_data`: when you **point your mouse** at a bubble, a box pops up with all their marks.

```python
slope, intercept = np.polyfit(df["Attendance"], df["Average"], 1)
xs = np.array([df["Attendance"].min(), df["Attendance"].max()])
fig.add_trace(go.Scatter(x=xs, y=slope * xs + intercept, mode="lines", name="Trend", line=dict(dash="dash", color="#6B7280")))
```
- `polyfit(..., 1)` finds the **best straight line** through the dots. That's the line `y = slope × x + intercept` from maths class!
- Then we draw that line from the lowest to the highest attendance.

```python
fig.add_annotation(x=top_row["Attendance"], y=top_row["Average"], text=f"🏆 Topper: {top_row['Student']}",
                   showarrow=True, arrowhead=2, ax=-70, ay=-35)
```
- Add an **arrow with a label** pointing at the topper's bubble.

```python
fig.update_traces(textposition="top center", textfont_size=9, selector=dict(mode="markers+text"))
fig.update_layout(xaxis_title="Attendance (%)", yaxis_title="Average marks", height=520, legend_title="Grade")
fig.write_html(f"{CHART_DIR}/09_bubble_chart.html", include_plotlyjs="cdn")
fig.show()
```
- Put the names **above** the bubbles, set the labels and height, **save as an interactive web page**, and show it.
  - `include_plotlyjs="cdn"` means "don't copy the whole plotly program into the file; load it from the internet." That keeps the file small.

### 8.9 Grouped bars with zoom slider

```python
fig = go.Figure()
for s in SUBJECTS:
    fig.add_bar(name=s, x=ranked["Student"], y=ranked[s], marker_color=SUBJECT_COLORS[s],
                hovertemplate="%{x}<br>" + s + ": %{y}<extra></extra>")
```
- Start with an empty plotly figure, then add one set of bars **for each subject**.
- `hovertemplate` decides what the hover box says. `%{x}` is the name, `%{y}` is the mark, `<br>` is a new line, and `<extra></extra>` hides an extra grey label.

```python
fig.add_scatter(name="Average", x=ranked["Student"], y=ranked["Average"], mode="lines+markers", line=dict(color="#111827", width=2))
```
- A black line with dots for the average.

```python
fig.update_layout(barmode="group", title="...",
                  xaxis=dict(title="Student", rangeslider=dict(visible=True), range=[-0.5, 9.5]),
                  yaxis=dict(title="Marks", range=[50, 100]), height=520, legend_title="Subject")
```
- `barmode="group"`: bars sit **side by side** instead of on top of each other.
- `rangeslider`: a mini chart underneath that you can **drag to zoom**.
- `range=[-0.5, 9.5]`: start by showing only the first 10 students.

### 8.10 Stacked area chart

```python
for s in SUBJECTS:
    fig.add_scatter(x=ranked["Student"], y=ranked[s], name=s, mode="lines", stackgroup="one", ...)
fig.update_layout(..., hovermode="x unified", height=460)
```
- `stackgroup="one"` **piles** each subject's area on top of the one below, like layers of a cake. The top edge is the total.
- `hovermode="x unified"`: hovering shows **all 4 subjects** for that student in one box.

### 8.11 & 8.12 Sunburst and Treemap

```python
fig = px.sunburst(df, path=["Grade", "Student"], values="Total", color="Grade", color_discrete_map=GRADE_COLORS, ...)
```
- A **sunburst** is a pie inside a pie. The inner ring shows **grades**, and the outer ring shows the **students** in each grade. Slice size = total marks. Click a slice to zoom in!

```python
fig = px.treemap(df, path=[px.Constant("Class"), "Grade", "Student"], values="Total", color="Average",
                 color_continuous_scale="Tealgrn", ...)
```
- A **treemap** is like a box of chocolates: one big box (the class), split into grade boxes, split again into student boxes. Darker colour = higher average.
- `px.Constant("Class")` makes one big parent box that holds everything.

### 8.13 Radar chart

```python
def radar_figure(names, title="Subject Profile"):
    cats = SUBJECTS + [SUBJECTS[0]]
```
- A **radar (spider-web)** chart has one spoke per subject.
- We add the first subject again at the end so the shape **closes** into a loop.

```python
    fig = go.Figure()
    fig.add_trace(go.Scatterpolar(r=[df[s].mean() for s in cats], theta=cats, name="Class average",
                                  line=dict(color="#9CA3AF", dash="dash")))
```
- Draw the **class average** shape as a grey dashed outline.
  - `r` is how far from the centre (the mark).
  - `theta` is which spoke (the subject).

```python
    colors = ["#4F46E5", "#E11D48", "#0EA5A4", "#F59E0B", "#7C3AED"]
    for name, color in zip(names, colors):
        row = df.set_index("Student").loc[name]
        fig.add_trace(go.Scatterpolar(r=[row[s] for s in cats], theta=cats, name=name, fill="toself",
                                      opacity=0.55, line=dict(color=color)))
```
- For each student we asked for, draw their shape, **filled in** (`fill="toself"`) and half see-through, each in a different colour.

```python
    fig.update_layout(title=title, polar=dict(radialaxis=dict(range=[50, 100])), height=480)
    return fig
```
- The web goes from 50 (centre) to 100 (outside). The recipe hands back the finished chart.

```python
fig = radar_figure([top_row["Student"], low_row["Student"]], "Topper vs Lowest Performer vs Class Average")
```
- Use it to compare Queen and David.

### 8.14 Altair linked charts

```python
alt_df = df[["Student", "Attendance", "Average", "Grade", "Total"]]
brush = alt.selection_interval(encodings=["x", "y"])
```
- `brush` is a **selection rectangle**: you drag across the chart to draw a box.

```python
grade_scale = alt.Scale(domain=GRADE_ORDER, range=[GRADE_COLORS[g] for g in GRADE_ORDER])
```
- Tell Altair which colour belongs to which grade.

```python
scatter = (alt.Chart(alt_df).mark_circle(size=110, opacity=0.85)
           .encode(x=alt.X("Attendance:Q", ...), y=alt.Y("Average:Q", ...),
                   color=alt.condition(brush, alt.Color("Grade:N", scale=grade_scale), alt.value("#D1D5DB")),
                   tooltip=[...])
           .add_params(brush)
           .properties(width=420, height=320, title="Drag to select students"))
```
- A scatter of circles:
  - `:Q` means **quantity** (a number) and `:N` means **name** (a category).
  - `alt.condition(brush, colour, grey)`: dots **inside** your box keep their colour, and the rest turn **grey**.
  - `.add_params(brush)` switches the box-drawing on.

```python
bars = (alt.Chart(alt_df).mark_bar()
        .encode(y=alt.Y("Student:N", sort="-x", title=None), x=alt.X("Average:Q", ...), color=..., tooltip=[...])
        .transform_filter(brush)
        .properties(width=300, height=320, title="Selected students"))
```
- A bar chart of averages. `transform_filter(brush)` is the magic part: it **only shows the students inside your box**. `sort="-x"` puts the biggest bar on top.

```python
alt.hconcat(scatter, bars).configure_title(fontSize=14, anchor="start")
```
- `hconcat` places the two charts **side by side** (h = horizontal).

---

## Section 9 – Interactive widgets (buttons and sliders) 🎛️

### 9.1 Student dropdown

```python
student_dd = widgets.Dropdown(options=sorted(df["Student"]), value=top_row["Student"], description="Student:",
                              layout=widgets.Layout(width="280px"))
```
- Make a **dropdown menu** listing every name in A-to-Z order (`sorted`). It starts on Queen.

```python
explorer_out = widgets.Output()
```
- An empty **screen area** where we'll draw the results.

```python
def render_student(change=None):
    name = student_dd.value
    r = df.set_index("Student").loc[name]
```
- The recipe that runs **every time you pick a name**. `student_dd.value` is whichever name is currently chosen.

```python
    with explorer_out:
        explorer_out.clear_output(wait=True)
```
- "Draw inside the screen area. First **wipe the old picture**." (`wait=True` avoids flickering.)

```python
        badge = " 🏆 <b style='color:#D97706'>CLASS TOPPER</b>" if r["Rank"] == 1 else ""
        display(HTML(f"<h3 ...>{name}{badge}</h3>" f"Rank <b>#{r['Rank']}</b> of {len(df)} · ..."))
```
- If it's the topper, add a gold trophy badge. Then show the name, rank, average, total, grade and attendance.

```python
        fig = go.Figure()
        fig.add_bar(x=SUBJECTS, y=[r[s] for s in SUBJECTS], name=name, marker_color=[...], text=[...], textposition="outside")
        fig.add_scatter(x=SUBJECTS, y=[df[s].mean() for s in SUBJECTS], name="Class average", mode="lines+markers", line=dict(color="#111827", dash="dash"))
        fig.update_layout(title=f"{name}: marks vs class average", yaxis=dict(range=[0, 105], title="Marks"), xaxis_title="Subject", height=380, margin=dict(t=50, b=40))
        fig.show()
```
- The **student's marks as bars**, with the **class average as a dashed line** on top, so you can see where they're above or below.

```python
        att = go.Figure()
        att.add_bar(y=["Attendance %", "Average marks"], x=[r["Attendance"], r["Average"]], orientation="h", name=name, marker_color="#4F46E5")
        att.add_bar(y=[...], x=[df["Attendance"].mean(), df["Average"].mean()], orientation="h", name="Class average", marker_color="#CBD5E1")
        att.update_layout(title="Attendance compared with average marks", barmode="group", xaxis=dict(range=[0, 100]), height=260, margin=dict(t=50, b=30))
        att.show()
```
- **Sideways bars** (`orientation="h"`) comparing this student's attendance and average with the class's. This is the "compare attendance with average marks" requirement.

```python
student_dd.observe(render_student, names="value")
render_student()
display(student_dd, explorer_out)
```
- `.observe(...)`: "**Watch** the dropdown. Whenever its value changes, run `render_student`." This is what makes it interactive!
- Then draw it once at the start, and show the dropdown and the screen area.

### 9.2 Compare students

```python
compare_sel = widgets.SelectMultiple(options=sorted(df["Student"]), value=("Queen", "David", "Michael"), description="Students:", rows=8, ...)
subject_sel = widgets.SelectMultiple(options=SUBJECTS, value=tuple(SUBJECTS), description="Subjects:", rows=4, ...)
compare_out = widgets.Output()
```
- Two lists where you can pick **several** items (hold Ctrl and click). One is for students, one is for subjects.

```python
def render_compare(change=None):
    names, subs = list(compare_sel.value), list(subject_sel.value)
    with compare_out:
        compare_out.clear_output(wait=True)
        if not names or not subs:
            print("Select at least one student and one subject.")
            return
```
- Get the choices. If nothing is chosen, show a friendly message and stop (`return` leaves the recipe early).

```python
        sub_df = (df.set_index("Student").loc[names, subs].reset_index()
                    .melt(id_vars="Student", var_name="Subject", value_name="Marks"))
        fig = px.bar(sub_df, x="Subject", y="Marks", color="Student", barmode="group", text="Marks", title="Student comparison", range_y=[0, 105])
        fig.update_layout(height=400)
        fig.show()
        radar_figure(names[:5], "Subject profile (all subjects)").show()
```
- Take only the chosen students and subjects, reshape them, and draw **grouped bars** (one colour per student).
- Also draw a radar chart. `names[:5]` takes **at most the first 5** names, so the web doesn't get too messy.

```python
for w in (compare_sel, subject_sel):
    w.observe(render_compare, names="value")
render_compare()
display(widgets.HBox([compare_sel, subject_sel]), compare_out)
```
- Watch **both** lists. `HBox` places the widgets side by side in a row.

### 9.3 Search, filter and sort

```python
search_box = widgets.Text(placeholder="Search student…", description="Search:", ...)
avg_slider = widgets.FloatRangeSlider(value=(60, 100), min=60, max=100, step=0.5, description="Average:", ...)
att_slider = widgets.IntSlider(value=80, min=80, max=100, description="Min attend.:", ...)
grade_dd = widgets.Dropdown(options=["All"] + GRADE_ORDER, value="All", description="Grade:", ...)
metric_dd = widgets.Dropdown(options=["Average"] + SUBJECTS + ["Attendance"], value="Average", description="Chart by:", ...)
sort_dd = widgets.Dropdown(options=["Rank", "Student", "Average", "Total", "Attendance"] + SUBJECTS, value="Rank", description="Sort by:", ...)
asc_tg = widgets.ToggleButtons(options=["Ascending", "Descending"], value="Ascending", ...)
filter_out = widgets.Output()
```
- 7 controls:
  - A **text box** for searching.
  - A **range slider** with two handles (minimum and maximum average).
  - A **single slider** for minimum attendance.
  - A **grade dropdown**.
  - A **"chart by" dropdown** (which number the bars show).
  - A **sort dropdown**.
  - **Two buttons** for small → big or big → small.

```python
def render_filtered(change=None):
    view = filter_students(min_avg=avg_slider.value[0], max_avg=avg_slider.value[1], min_attendance=att_slider.value,
                           grade=None if grade_dd.value == "All" else grade_dd.value, search=search_box.value)
```
- Read every control and pass the values to our `filter_students` recipe from Section 7.5.
  - `avg_slider.value[0]` is the left handle and `[1]` is the right handle.
  - `None if ... == "All" else ...`: if "All" is chosen, don't filter by grade.

```python
    view = view.sort_values(sort_dd.value, ascending=asc_tg.value == "Ascending")
```
- Sort by the chosen column. `asc_tg.value == "Ascending"` is True or False, which tells pandas the direction.

```python
    with filter_out:
        filter_out.clear_output(wait=True)
        print(f"Showing {len(view)} of {len(df)} students")
        if view.empty:
            return
        display(view.style.apply(highlight_extremes, axis=1).format({"Average": "{:.2f}"}).hide(axis="index"))
```
- Wipe the area, say how many students matched, stop if none matched, and otherwise show the coloured table.

```python
        metric = metric_dd.value
        colors = ["#D97706" if r == 1 else "#4F46E5" for r in view["Rank"]]
        fig = go.Figure(go.Bar(x=view["Student"], y=view[metric], marker_color=colors, text=view[metric], textposition="outside", hovertemplate=...))
        fig.update_layout(title=f"{metric} for the filtered students (gold = topper)", ...)
        fig.show()
```
- A bar chart of the filtered students. The topper's bar is **gold**, and everyone else's is purple.

```python
for w in (search_box, avg_slider, att_slider, grade_dd, metric_dd, sort_dd, asc_tg):
    w.observe(render_filtered, names="value")
render_filtered()
display(widgets.VBox([widgets.HBox([search_box, grade_dd, metric_dd]),
                      widgets.HBox([avg_slider, att_slider]),
                      widgets.HBox([sort_dd, asc_tg])]), filter_out)
```
- Watch **all 7 controls**: change any of them and the table and chart update instantly.
- `VBox` stacks rows on top of each other, and `HBox` lines things up side by side. Together they make a neat **control panel**.

---

## Section 10 – Insights 💡

```python
top5 = ranked.head(5)[["Rank", "Student", "Average", "Grade", "Attendance"]]
bottom5 = ranked.tail(5).iloc[::-1][["Rank", "Student", "Average", "Grade", "Attendance"]]
```
- `head(5)` takes the **first 5** rows (the best students). `tail(5)` takes the **last 5**.
- `iloc[::-1]` **reverses** the order, so the very lowest student comes first.

```python
display(HTML("<h4>🥇 Top 5 performers</h4>")); display(top5.style.hide(axis="index").format({"Average": "{:.2f}"}))
display(HTML("<h4>📉 Bottom 5 performers (lowest first)</h4>")); display(bottom5.style...)
```
- Show a heading and a table for each.

```python
subject_summary = pd.DataFrame({
    "Average": df[SUBJECTS].mean().round(2),
    "Highest": df[SUBJECTS].max(),
    "Lowest": df[SUBJECTS].min(),
    "Std dev": df[SUBJECTS].std().round(2),
    "Corr. with attendance": [round(df[s].corr(df["Attendance"]), 2) for s in SUBJECTS],
    "Top scorer": [df.loc[df[s].idxmax(), "Student"] for s in SUBJECTS],
}).sort_values("Average", ascending=False)
```
- A **report card for each subject**:
  - The average, the highest and lowest mark.
  - **Std dev** (standard deviation): how spread out the marks are. Small means everyone scored about the same.
  - How strongly the subject is linked to attendance.
  - Who got the top mark: `idxmax` finds the row number, and `.loc[..., "Student"]` turns it into a name.
- Finally, sort the subjects from best to worst.

```python
best_subject, worst_subject = subject_summary.index[0], subject_summary.index[-1]
```
- The first row is the **best subject** (AI) and the last row is the **worst** (Python). Two boxes get filled at once.

```python
att_bands = pd.cut(df["Attendance"], bins=[0, 89, 94, 100], labels=["< 90%", "90–94%", "95–100%"])
```
- `pd.cut` puts each student into an **attendance group**, like sorting socks into 3 drawers.

```python
att_table = (df.groupby(att_bands, observed=True)
               .agg(Students=("Student", "count"), Avg_marks=("Average", "mean")).round(2))
```
- For each drawer: **how many students** are in it, and **what's their average mark**. This shows that better attendance goes with better marks.
  - `groupby` means "make piles", and `agg` means "calculate something for each pile".

```python
print(f"Pearson correlation between attendance and average marks: r = {att_corr:.2f}")
```
- Prints r = 0.93.

```python
perfect = df.loc[df["Attendance"] == 100, "Student"].tolist()
```
- The names of students with **exactly 100%** attendance, as a plain list. That's only Queen.

```python
needs_help = ranked.loc[ranked["Status"] == "Needs Improvement", ["Student", "Average", "Weakest Subject", "Attendance"]]
needs_help = needs_help.assign(**{"Weakest mark": [df.set_index("Student").loc[n, s]
                                                   for n, s in zip(needs_help["Student"], needs_help["Weakest Subject"])]})
```
- Keep only the students marked "Needs Improvement".
- `.assign(...)` adds a new column, **"Weakest mark"**: for each student `n` and their weakest subject `s`, look up the mark. (`**{...}` lets us use a column name that has a space in it.)

```python
display(HTML(f"<h4>🎯 Students with perfect attendance</h4><p>{', '.join(perfect) or 'None'}</p>"))
display(HTML(f"<h4>🛠️ Students requiring improvement (average &lt; {NEEDS_HELP_AVG} or any subject &lt; {NEEDS_HELP_SUBJECT})</h4>"))
display(needs_help.style.hide(axis="index").format({"Average": "{:.2f}"}))
```
- Show the results.
  - `or 'None'` means: if the list is empty, write "None".
  - `&lt;` is how HTML writes the `<` sign.

### 10.1 Sentences written automatically

```python
def strength(r):
    a = abs(r)
    return "very strong" if a >= 0.8 else "strong" if a >= 0.6 else "moderate" if a >= 0.4 else "weak"
```
- Turns a correlation number into **words**. `abs` removes any minus sign. 0.93 becomes "very strong".

```python
high_att_avg = df.loc[df["Attendance"] >= 95, "Average"].mean()
low_att_avg = df.loc[df["Attendance"] < 90, "Average"].mean()
```
- The average mark of the **high-attendance** students (91.0) and the **low-attendance** students (73.9).

```python
insights = [
    f"{top_row['Student']} is the class topper with an average of {top_row['Average']:.2f} and ...",
    ...
]
```
- A list of **8 sentences** with holes filled in from our numbers. If the data changes, the sentences change too, like a **Mad Libs** game where the computer fills in the blanks.

```python
display(Markdown("\n".join(f"- {line}" for line in insights)))
```
- Put a `- ` in front of each sentence (which makes a bullet point), join them with new lines, and show them.

---

## Section 11 – Building the HTML dashboard 🌐

```python
DASHBOARD_TEMPLATE = r'''<!doctype html> ... '''
print(f"Template loaded ({len(DASHBOARD_TEMPLATE):,} characters)")
```
- A **very long string** holding a whole web page: HTML (the structure), CSS (the colours and layout) and JavaScript (the buttons that work).
- It's a **template**, like a form with a blank space. The blank is the word `__DASHBOARD_DATA__`.
- `r'''...'''` is a **raw** string, which means "don't treat backslashes `\` specially."
- `{:,}` prints the number with commas, like 30,000.

*(What's inside the template is explained in the "Inside the HTML file" section below.)*

```python
def build_payload():
    records = ranked[["Rank", "Student", *SUBJECTS, "Attendance", "Total", "Average", "Grade",
                      "Best Subject", "Weakest Subject", "Status", "Imputed"]]
```
- A recipe that **packs a lunchbox** of all the data the web page needs. First it picks the columns.

```python
    return {
        "generated": datetime.now().strftime("%d %b %Y, %H:%M"),
```
- Today's date and time, written like `30 Oct 2026, 10:01`. `strftime` formats the date: `%d` is the day, `%b` the month name, `%Y` the year, `%H:%M` the hours and minutes.

```python
        "subjects": SUBJECTS,
        "grades": GRADE_ORDER,
        "thresholds": {"high": HIGH_SCORE, "low": LOW_SCORE},
        "students": json.loads(records.to_json(orient="records")),
```
- The subject list, the grade list and our 85/60 rules.
- `records.to_json(orient="records")` turns the table into JSON text, with **one `{...}` per student**. Then `json.loads` turns that text back into normal Python lists. This round trip makes sure every number is in a plain format the web page can read.

```python
        "kpis": kpis,
        "insights": insights,
        "issues": json.loads(issue_log.to_json(orient="records")),
        "validationRaw": json.loads(validate(dedup_df).to_json(orient="records")),
        "validationClean": json.loads(validation_clean.to_json(orient="records")),
        "quality": {
            "rows": int(len(raw_df)),
            "missing": int(raw_df.isna().sum().sum()),
            "zeros": int(zero_mask.sum().sum()),
            "duplicates": int(len(raw_df) - len(dedup_df)),
            "outliersRaw": int(len(raw_outliers)),
            "outliersClean": int(len(clean_outliers)),
        },
    }
```
- Everything else goes into the lunchbox: the KPI numbers, the insight sentences, the cleaning diary, the pass/fail checks, and the data-quality counts.

```python
payload_json = json.dumps(build_payload(), ensure_ascii=False).replace("</", "<\\/")
```
- `json.dumps` turns the lunchbox into **one long line of JSON text**. `ensure_ascii=False` keeps emojis and special letters as they are.
- `.replace("</", "<\/")` is a **safety trick**: inside a web page, the text `</` could accidentally end the script early, so we add a harmless backslash.

```python
dashboard_html = DASHBOARD_TEMPLATE.replace("__DASHBOARD_DATA__", payload_json)
```
- **Fill in the blank** in the template with our data. Now the web page has everything it needs.

```python
with open(DASHBOARD_PATH, "w", encoding="utf-8") as f:
    f.write(dashboard_html)
print(f"Saved {DASHBOARD_PATH} ({os.path.getsize(DASHBOARD_PATH) / 1024:.1f} KB)")
```
- Save it as `student_dashboard.html`, and print its size. `getsize` gives bytes, and `/ 1024` turns them into kilobytes.

```python
from html import escape
display(HTML(f'<iframe srcdoc="{escape(dashboard_html)}" '
             'style="width:100%;height:900px;border:1px solid #E5E7EB;border-radius:12px"></iframe>'))
```
- Show the dashboard **inside the notebook**, in a little window called an `iframe` (a web page inside a web page).
- `escape(...)` makes the HTML safe to put inside quotes (for example, `"` becomes `&quot;`).

---

## Section 12 – Saving and downloading 💾

```python
export_cols = ["Rank", "Student", *SUBJECTS, "Attendance", "Total", "Average", "Grade",
               "Best Subject", "Weakest Subject", "Status", "Imputed"]
ranked[export_cols].to_csv(PROCESSED_PATH, index=False)
issue_log.to_csv("data_cleaning_log.csv", index=False)
```
- Save the **clean, ranked table** and the **cleaning diary** as CSV files. `index=False` means "don't save the 0, 1, 2… row numbers."

```python
print(f"Saved {PROCESSED_PATH} and data_cleaning_log.csv\n")
display(pd.read_csv(PROCESSED_PATH).head())
```
- Read the saved file back and show its first 5 rows, **proof** that it saved correctly.

```python
print("\nGenerated files:")
for path in [CSV_PATH, PROCESSED_PATH, "data_cleaning_log.csv", DASHBOARD_PATH] + sorted(
        os.path.join(CHART_DIR, f) for f in os.listdir(CHART_DIR)):
    print(f"  {path:45s} {os.path.getsize(path) / 1024:7.1f} KB")
```
- List **every file we made**, with its size.
  - `os.listdir` lists what's inside the charts folder, and `os.path.join` builds paths like `charts/02_subject_average_bar.png`.
  - `{path:45s}` pads the name to 45 characters so the sizes line up neatly in a column.

```python
if IN_COLAB:
    import shutil
    from google.colab import files
    bundle_dir = "student_dashboard_outputs"
    os.makedirs(bundle_dir, exist_ok=True)
    for path in [CSV_PATH, PROCESSED_PATH, "data_cleaning_log.csv", DASHBOARD_PATH]:
        shutil.copy(path, bundle_dir)
    shutil.copytree(CHART_DIR, os.path.join(bundle_dir, CHART_DIR), dirs_exist_ok=True)
    shutil.make_archive(bundle_dir, "zip", bundle_dir)
    files.download(f"{bundle_dir}.zip")
else:
    print("Not running in Colab: the files are in the current folder.")
```
- **Only in Colab**:
  1. `shutil` is the file mover toolbox, and `files` is Colab's download button.
  2. Make a folder called `student_dashboard_outputs`.
  3. **Copy** the 4 main files into it.
  4. Copy the whole charts folder in too (`copytree` = copy a folder with everything inside).
  5. `make_archive(..., "zip", ...)` **squeezes** it all into one `.zip` file, like packing a suitcase.
  6. `files.download(...)` sends the zip to your computer.
- **Otherwise** (not in Colab), just say where the files are.

---

## 🌐 Inside the HTML file (the dashboard's three languages)

A web page is written in **3 languages**, like a house:

| Language | House version | In our file |
|---|---|---|
| **HTML** | The walls and rooms | `<header>`, `<nav>` (the tab buttons), 5 `<section>`s (one per page), and empty `<div id="chSubjectAvg">` boxes waiting for charts |
| **CSS** (`<style>`) | The paint and furniture | Colours stored as **variables** like `--accent`; `:root[data-theme="dark"]` swaps every colour for dark mode; `.grid`/`.g2` arranges cards in 2 columns; `@media (max-width: 960px)` switches to 1 column on small screens (that's the **responsive design**) |
| **JavaScript** (`<script>`) | The electricity: switches that work | The code that draws the charts and reacts to clicks |

**The JavaScript, part by part:**

| Code | What it does |
|---|---|
| `const D = {...data...}` | Opens the lunchbox the Python code packed. `S` is the students, `SUBJ` is the subjects. |
| `const $ = id => document.getElementById(id)` | A shortcut: `$("kpis")` means "find the box called kpis on the page." |
| `mean`, `fmt`, `esc` | Small helpers: average, round a number for display, and make text safe to show. |
| `topper = S[0]`, `lowest = ...` | The students are already sorted, so the first one is the topper. |
| `classAvg` | The class average for every subject. |
| `function layout(title, extra)` | The shared **chart style** (fonts, grid colours, legend position), so all charts look alike. It reads colours from CSS, which is why the charts change in dark mode too. |
| `plot(id, data, lay)` | Calls `Plotly.react(...)` to draw a chart inside a box. |
| `renderOverview()` | Fills the **Overview** page: 7 KPI cards, the topper banner, the subject bars, the grade donut, the bubble chart with trend line (the slope and the `r` value are calculated with the same maths as the Python), the histogram, the heatmap and the insights. |
| `renderStudent()` | The **Student** page: reads the dropdown, builds the profile card (▲ green if above the class average, ▼ red if below), the bar chart, the radar and the attendance bars. |
| `renderCompare()` | The **Compare** page: Student A vs B bars, the radar, one subject across the class (A and B shown as bigger dots), the box plot and the stacked area. |
| `filteredStudents()` | The **search and filter sieve**: name contains the typed text, average ≥ slider, attendance ≥ slider, grade matches. Then it sorts. |
| `renderRankings()` | Builds the **table** (gold row for the topper, red for the lowest), makes column headers clickable to sort, makes rows clickable to open that student, and draws the bar chart. |
| `renderQuality()` | The **Data Quality** page: counts, the pass/fail table and the cleaning diary. |
| `showTab(name)` | Shows one page and hides the other four, like **turning pages in a book**. |
| `setTheme(theme)` | Switches **dark/light** mode, remembers your choice (`localStorage`), and redraws the charts. |
| `downloadCSV()` | Builds a CSV from the data and makes your browser **download** it. |
| `init()` | Runs once at the start: fills every dropdown, connects every button to its function (`onchange`, `oninput`, `onclick`), picks the theme, and opens the first page. |
| `document.fonts.ready.then(init)` | **Wait until the font has loaded** before drawing, so the chart labels aren't cut off. |

---

## 🎤 If your teacher asks you to explain it in 30 seconds

> "I read the marks from a CSV file with pandas. I found 5 missing values and 4 zeros. A zero can't be a real mark, so I replaced each one with that student's own average, and missing attendance with the class median. Then I calculated Total, Average, Grade and Rank. I made charts with four libraries (Matplotlib, Seaborn, Plotly and Altair) and added dropdowns and sliders with ipywidgets. Finally, I packed all the data into one HTML file. It's a 5-page dashboard with search, filters, dark mode and CSV export. Queen is the topper with 95.75, and attendance is strongly linked to marks: r = 0.93."
