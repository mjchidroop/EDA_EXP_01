# IPL Dataset Analysis 

### Developed by: Chidroop M J
### Registered number: 212225240029

## Experiment Title
**Exploratory Data Analysis (EDA) and Data Preprocessing on the IPL Matches Dataset**

---

## Aim
To perform dataset understanding, data cleaning, exploratory data analysis (EDA), and data transformation on the IPL matches dataset, deriving insights about matches per season, team performance, toss decisions, top venues, winning margins, and match result types.

---

## Dataset
- **File:** `matches.csv`
- **Records:** 1095 rows
- **Columns:** 21 (including `id`, `season`, `city`, `date`, `team1`, `team2`, `toss_winner`, `toss_decision`, `winner`, `result`, `result_margin`, `venue`, etc.)

---

## Tools & Libraries
- **Python 3.10**
- **pandas** — data manipulation and analysis
- **numpy** — numerical operations
- **matplotlib / seaborn** — (optional) visualization

---

## Algorithm / Procedure

1. **Import Libraries** — Import `pandas` and `numpy` for data handling and numerical operations.
2. **Load Dataset** — Read `matches.csv` using `pd.read_csv()`.
3. **Dataset Understanding** — Display the shape (rows × columns) and the first 5 records using `.shape` and `.head()`; display column names and data types using `.dtypes`.
4. **Primary Key Verification** — Check uniqueness of the `id` column using `is_unique` and `duplicated().sum()`.
5. **Data Cleaning** — Identify missing values using `.isnull().sum()`; fill missing values in the `city` column with `"Unknown"`; remove duplicate records using `.drop_duplicates()`.
6. **Matches per Season** — Group data by `season`, count matches using `.groupby()["id"].count()`, sort in descending order, and identify the season with the highest number of matches.
7. **Top Winning Teams** — Apply `value_counts()` on the `winner` column and display the top 5 teams.
8. **CSK Wins (Filtering)** — Filter rows where `winner == "Chennai Super Kings"` and display match details along with the total count.
9. **Toss Decision Analysis** — Use `value_counts()` and `value_counts(normalize=True)` on `toss_decision` to compute frequency and percentage; identify the most preferred decision.
10. **Season-wise Toss Analysis** — Apply `pd.crosstab()` on `season` vs `toss_decision` and use `idxmax(axis=1)` to find the most preferred decision per season.
11. **Top Venues** — Apply `value_counts()` on `venue` and display the top 5 venues.
12. **Winning Margin Analysis** — Create a `win_margin` column from `result_margin`; find the match with the largest margin using `idxmax()` and display the top 10 matches using `.sort_values()`.
13. **Match Result Types** — Apply `value_counts()` on the `result` column to find frequency of runs, wickets, tie, and no result.
14. **Datetime Conversion** — Convert `date` column using `pd.to_datetime()` and display data type before and after conversion.
15. **Year Extraction** — Create a new `year` column using `df["date"].dt.year`.
16. **Create `win_type` Column** — Apply a custom function mapping `result` values (`runs`, `wickets`, `tie`, `no result`) to meaningful descriptions (`Won by Runs`, `Won by Wickets`, `Tie`, `No Result`).
17. **Final Verification** — Display dataset shape and sample of newly created columns.

---

## Program

```python
import pandas as pd
import numpy as np

df = pd.read_csv("matches.csv")

# 1. Dataset Understanding
print("Rows and Columns:", df.shape)
df.head()

# 2. Columns and Data Types
print(df.dtypes)

# 3. Primary Key Verification
print("Is 'id' unique?", df["id"].is_unique)
print("Duplicate IDs:", df["id"].duplicated().sum())

# 4. Data Cleaning
print(df.isnull().sum())
df["city"] = df["city"].fillna("Unknown")
before = df.shape[0]
df = df.drop_duplicates()
after = df.shape[0]
print("Duplicates removed:", before - after)
print("Missing in city:", df["city"].isnull().sum())

# 5. Matches per Season
matches_per_season = df.groupby("season")["id"].count().sort_values(ascending=False)
print(matches_per_season)
print("Highest:", matches_per_season.idxmax(), "->", matches_per_season.max())

# 6. Top 5 Winning Teams
top5_teams = df["winner"].value_counts().head(5)
print(top5_teams)

# 7. CSK Wins
csk_wins = df[df["winner"] == "Chennai Super Kings"]
print(csk_wins[["id", "season", "team1", "team2", "winner", "venue"]].head())
print("Total CSK wins:", csk_wins.shape[0])

# 8. Toss Decision Frequency & Percentage
toss_counts = df["toss_decision"].value_counts()
toss_pct = (df["toss_decision"].value_counts(normalize=True) * 100).round(2)
print(pd.DataFrame({"Count": toss_counts, "Percentage": toss_pct}))
print("Most preferred:", toss_counts.idxmax())

# 9. Season-wise Toss Decisions
toss_cross = pd.crosstab(df["season"], df["toss_decision"])
print(toss_cross)
print(toss_cross.idxmax(axis=1))

# 10. Top 5 Venues
top5_venues = df["venue"].value_counts().head(5)
print(top5_venues)

# 11. Largest Winning Margin
margin_col = "result_margin" if "result_margin" in df.columns else "margin"
df["win_margin"] = df[margin_col].fillna(0)
print("Largest margin match:")
print(df.loc[df["win_margin"].idxmax(),
             ["id", "season", "team1", "team2", "winner",
              "result", margin_col, "win_margin"]])
print("\nTop 10:")
print(df.sort_values("win_margin", ascending=False).head(10)
        [["id", "season", "winner", "result", margin_col, "win_margin"]])

# 12. Match Result Types
print(df["result"].value_counts())

# 13. Datetime Conversion
print("Before:", df["date"].dtype)
df["date"] = pd.to_datetime(df["date"], errors="coerce")
print("After :", df["date"].dtype)

# 14. Extract Year
df["year"] = df["date"].dt.year
print(df[["date", "year"]].head())

# 15. Create win_type Column
def get_win_type(row):
    r = row["result"]
    if r == "tie":       return "Tie"
    if r == "no result": return "No Result"
    if r == "runs":      return "Won by Runs"
    if r == "wickets":   return "Won by Wickets"
    return "Unknown"

df["win_type"] = df.apply(get_win_type, axis=1)
print(df[["id", "winner", "result", "win_type"]].head(10))
print("\nwin_type counts:")
print(df["win_type"].value_counts())

# Final Verification
print(df.shape)
cols = [c for c in ["id", "date", "year", "win_margin", "win_type"] if c in df.columns]
print(df[cols].head())
```

---

## Outputs:
### Rows and Columns:  
<img width="1757" height="383" alt="image" src="https://github.com/user-attachments/assets/bbc822bc-8055-49fe-8153-8c3c1cbfede6" />

### Datatypes:  
<img width="1850" height="372" alt="image" src="https://github.com/user-attachments/assets/cc01c290-8e78-4c5a-a61b-820a079194e0" />

### Primary key verification:  
<img width="1859" height="59" alt="image" src="https://github.com/user-attachments/assets/5777bf1a-d91f-408e-b59a-a2db0a59b895" />

### Data Cleaning:  
<img width="1847" height="393" alt="image" src="https://github.com/user-attachments/assets/d2a91cc5-2cc2-4eea-8144-21fe6691842f" />

### Matches per season:  
<img width="1858" height="352" alt="image" src="https://github.com/user-attachments/assets/256ec3d2-9574-488b-9ee4-6000e825a4dd" />

### Top five winning teams:  
<img width="1861" height="136" alt="image" src="https://github.com/user-attachments/assets/330c6912-3c17-493d-80f6-96f35a078b39" />

### CSK wins:  
<img width="1857" height="263" alt="image" src="https://github.com/user-attachments/assets/9f96f58e-e983-46d7-957a-9f6d0e617701" />

### Toss Decision Frequency & Percentage:  
<img width="1859" height="111" alt="image" src="https://github.com/user-attachments/assets/6c770024-2dc4-4989-ac67-0ce288cf96f2" />

### Season-wise Toss Decisions:  
<img width="1859" height="645" alt="image" src="https://github.com/user-attachments/assets/d67ae8c8-3eb7-4cf5-b660-6b6b6ef6dd35" />

### Top 5 Venues:  
<img width="1869" height="145" alt="image" src="https://github.com/user-attachments/assets/93f1229b-730b-4ac9-b01e-51944971d078" />

### Largest Winning Margin:  
<img width="1861" height="602" alt="image" src="https://github.com/user-attachments/assets/2c1c0e85-5585-4378-98db-c85c380d04c4" />

### Match Result Types:  
<img width="1852" height="129" alt="image" src="https://github.com/user-attachments/assets/73b7ff39-8484-4ef2-b70d-ad154735f2d8" />

### Datetime Conversion:  
<img width="1861" height="125" alt="image" src="https://github.com/user-attachments/assets/5bf2fb10-aa48-4a14-86b3-7f652e9167bd" />

### Extract Year:  
<img width="1859" height="125" alt="image" src="https://github.com/user-attachments/assets/fb4ffa86-2191-460a-91c8-9931aef6299e" />

### Create win_type Column:  
<img width="1853" height="336" alt="image" src="https://github.com/user-attachments/assets/3f4d3c08-cc39-47dc-8138-c3b2f60bfce9" />

### Verification:  
<img width="1859" height="131" alt="image" src="https://github.com/user-attachments/assets/c88bb8c2-f021-43f6-bc5b-224a625cdb2b" />

## Result

The experiment was executed successfully. Dataset understanding, cleaning, group-by analysis, filtering, cross-tabulation, aggregation, and data transformations — were completed. The dataset is now clean and enriched with new features (`year`, `win_margin`, `win_type`), revealing that Mumbai Indians are the most successful team, fielding first is the dominant toss choice, and M. Chinnaswamy Stadium, Eden Gardens, and Wankhede Stadium are the top IPL venues.
