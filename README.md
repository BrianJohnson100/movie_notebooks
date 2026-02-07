# Box Office Mojo Weekend Scraper (Year + Week)

## Overview
This notebook pulls the **Weekend Box Office** table from **Box Office Mojo** for a specific **year** and **week-of-year**, then:
- builds the correct Box Office Mojo weekend URL,
- loads the first HTML table on the page into a Pandas DataFrame,
- adds a `Datestamp` column (run timestamp),
- prints a few quick sanity checks (columns + rank/release),
- exports the results to an Excel file.

> Data source: Box Office Mojo weekend pages, e.g. `https://www.boxofficemojo.com/weekend/2026W05/`.

---

## Requirements
### Python packages
The notebook imports the following packages:
- `pandas`
- `datetime` (standard library)
- `pathlib` (standard library)
- `lxml` (used by `pandas.read_html`)
- `openpyxl` (Excel writer engine)

Install dependencies (example):
```bash
pip install pandas lxml openpyxl
```

---

## How it works
### 1) Define base URL
The notebook starts from the weekend endpoint:
```python
url = "https://www.boxofficemojo.com/weekend/"
```

### 2) Collect user inputs
It prompts for:
- **Year** (e.g., `2026`)
- **Week** (two digits, e.g., `05`)

```python
year = input("What year do you want to query on? ")
week = input("What week do you want to query on? Input format: ##")
```

### 3) Build the time-period slug
Box Office Mojo weekend pages use the format:
- `YYYYW##`  → example: `2026W05`

```python
timeperiod = year + 'W' + week
url = "https://www.boxofficemojo.com/weekend/" + timeperiod + "/"
```

### 4) Read the weekend table into Pandas
The first table on the page is loaded into a DataFrame:
```python
movie_df = pd.read_html(url)[0]
```

### 5) Add a datestamp column
A timestamp is appended so you can track when the data was pulled:
```python
dt_now = datetime.datetime.now()
movie_df["Datestamp"] = dt_now
```

### 6) Quick checks
The notebook prints:
- the DataFrame column names
- a small view of `Rank` and `Release` (useful for verifying the scrape)
```python
print(movie_df.columns)
print(movie_df[["Rank", "Release"]])
```

### 7) Export to Excel
Finally, it writes the full DataFrame to an `.xlsx` file:
```python
Location_for_file_export2 = r"C:\Users\Brian\PycharmProjects\notebooks\data_output\Box_Office1.xlsx"
movie_df.to_excel(Location_for_file_export2, index=False)
```

---

## Usage
### Run inside Jupyter
1. Open `Box_Office_Best.ipynb`.
2. Run all cells.
3. When prompted:
   - enter the year (e.g., `2026`)
   - enter the week number in two-digit format (e.g., `05`)

The notebook will print the generated URL and a preview of the scraped data.

### Expected output columns
Based on the scrape, the table typically includes fields like:
- `Rank`, `LW`, `Release`, `Gross`, `%± LW`, `Theaters`, `Change`, `Average`,
  `Total Gross`, `Weeks`, `Distributor`, plus metadata columns such as
  `New This Week`, `Estimated`, and the added `Datestamp`.

(Exact columns can vary if the website changes its table schema.)

---

## Notes and recommendations
- **File export path is currently hard-coded** to a Windows path.  
  If you run the notebook elsewhere, update `Location_for_file_export2` to a valid location on your machine.
- **Website structure can change**. If Box Office Mojo modifies their HTML tables, `pd.read_html()` may fail or return different columns.
- Consider adding:
  - input validation (ensure week is `01`–`53` and left-padded),
  - error handling for missing pages / rate limits,
  - a configurable output folder (e.g., via `Path`),
  - optional CSV export.

---

## Example
If the user enters:
- `year = 2026`
- `week = 05`

The notebook generates:
- `timeperiod = 2026W05`
- URL: `https://www.boxofficemojo.com/weekend/2026W05/`

Then it pulls the weekend table into `movie_df` and exports it to Excel.

---

## License / Terms
If you plan to use this at scale or commercially, review Box Office Mojo’s **terms of service** and be mindful of request rates.
