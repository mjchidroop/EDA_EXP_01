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

<img width="1757" height="383" alt="image" src="https://github.com/user-attachments/assets/bbc822bc-8055-49fe-8153-8c3c1cbfede6" />

```text
Q1:
Rows and Columns: (1095, 20)

        id   season        city        date match_type player_of_match  \
0  335982  2007/08   Bangalore  2008-04-18     League     BB McCullum   
1  335983  2007/08  Chandigarh  2008-04-19     League      MEK Hussey   
2  335984  2007/08       Delhi  2008-04-19     League     MF Maharoof   
3  335985  2007/08      Mumbai  2008-04-20     League     MV Boucher   
4  335986  2007/08     Kolkata  2008-04-20     League      DJ Hussey   

                                      venue  \
0                       M Chinnaswamy Stadium   
1  Punjab Cricket Association Stadium, Mohali   
2                          Feroz Shah Kotla   
3                          Wankhede Stadium   
4                             Eden Gardens   

                         team1                  team2  \
0  Royal Challengers Bangalore  Kolkata Knight Riders   
1              Kings XI Punjab    Chennai Super Kings   
2             Delhi Daredevils       Rajasthan Royals   
3              Mumbai Indians  Royal Challengers Bangalore   
4        Kolkata Knight Riders         Deccan Chargers   

                 toss_winner toss_decision                     winner   result  \
0  Royal Challengers Bangalore         field     Kolkata Knight Riders    runs   
1          Chennai Super Kings           bat    Chennai Super Kings     runs   
2             Rajasthan Royals           bat       Delhi Daredevils  wickets   
3              Mumbai Indians           bat  Royal Challengers Bangalore  wickets   
4             Deccan Chargers           bat    Kolkata Knight Riders   wickets   

   result_margin  target_runs  target_overs super_over  method  umpire1  \
0          140.0        223.0          20.0          N     NaN  Asad Rauf   
1           33.0        241.0          20.0          N     NaN  MR Benson   
2            9.0        130.0          20.0          N     NaN  Aleem Dar   
3            5.0        166.0          20.0          N     NaN   SJ Davis   
4            5.0        111.0          20.0          N     NaN  BF Bowden   

         umpire2  
0      RE Koertzen  
1      SL Shastri  
2  GA Pratapkumar  
3       DJ Harper  
4    K Hariharan  

Q2:
id                   int64
season              object
city                object
date                object
match_type          object
player_of_match     object
venue               object
team1               object
team2               object
toss_winner         object
toss_decision       object
winner              object
result              object
result_margin      float64
target_runs        float64
target_overs       float64
super_over          object
method              object
umpire1             object
umpire2             object
dtype: object

Q3:
Is 'id' unique? True
Duplicate IDs: 0

Q4:
id                    0
season                0
city                 51
date                  0
match_type            0
player_of_match       5
venue                 0
team1                 0
team2                 0
toss_winner           0
toss_decision         0
winner                5
result                0
result_margin        19
target_runs           3
target_overs          3
super_over            0
method             1074
umpire1               0
umpire2               0
dtype: int64
Duplicates removed: 0
Missing in city: 0

Q5:
season
2013       76
2022       74
2012       74
2023       74
2011       73
2024       71
2019       60
2016       60
2009/10    60
2021       60
2020/21    60
2014       60
2018       60
2017       59
2015       59
2007/08    58
2009       57
Name: id, dtype: int64
Highest: 2013 -> 76

Q6:
winner
Mumbai Indians                 144
Chennai Super Kings            138
Kolkata Knight Riders          131
Royal Challengers Bangalore    116
Rajasthan Royals               112
Name: count, dtype: int64

Q7:
        id   season                        team1                  team2  \
1   335983  2007/08              Kings XI Punjab    Chennai Super Kings   
7   335989  2007/08          Chennai Super Kings         Mumbai Indians   
11  335993  2007/08          Chennai Super Kings  Kolkata Knight Riders   
14  335996  2007/08  Royal Challengers Bangalore    Chennai Super Kings   
27  336009  2007/08             Delhi Daredevils    Chennai Super Kings   

                 winner                                       venue  
1   Chennai Super Kings  Punjab Cricket Association Stadium, Mohali  
7   Chennai Super Kings             MA Chidambaram Stadium, Chepauk  
11  Chennai Super Kings             MA Chidambaram Stadium, Chepauk  
14  Chennai Super Kings                       M Chinnaswamy Stadium  
27  Chennai Super Kings                            Feroz Shah Kotla  
Total CSK wins: 138

Q8:
               Count  Percentage
toss_decision                   
field            704       64.29
bat              391       35.71
Most preferred: field

Q9:
toss_decision  bat  field
season                   
2007/08         26     32
2009            35     22
2009/10         39     21
2011            25     48
2012            37     37
2013            45     31
2014            19     41
2015            25     34
2016            11     49
2017            11     48
2018            10     50
2019            10     50
2020/21         27     33
2021            16     44
2022            15     59
2023            21     53
2024            19     52
season
2007/08    field
2009         bat
2009/10      bat
2011       field
2012         bat
2013         bat
2014       field
2015       field
2016       field
2017       field
2018       field
2019       field
2020/21    field
2021       field
2022       field
2023       field
2024       field
dtype: object

Q10:
venue
Eden Gardens                                 77
Wankhede Stadium                             73
M Chinnaswamy Stadium                        65
Feroz Shah Kotla                             60
Rajiv Gandhi International Stadium, Uppal    49
Name: count, dtype: int64

Q11:
Largest margin match:
id                        1082635
season                       2017
team1            Delhi Daredevils
team2              Mumbai Indians
winner             Mumbai Indians
result                       runs
result_margin               146.0
win_margin                    146
Name: 620, dtype: object

Top 10:
           id   season                       winner result  result_margin  \
620   1082635     2017               Mumbai Indians   runs          146.0   
560    980987     2016  Royal Challengers Bangalore   runs          144.0   
0      335982  2007/08        Kolkata Knight Riders   runs          140.0   
496    829785     2015  Royal Challengers Bangalore   runs          138.0   
352    598027     2013  Royal Challengers Bangalore   runs          130.0   
706   1175366     2019          Sunrisers Hyderabad   runs          118.0   
1009  1359534     2023  Royal Challengers Bangalore   runs          112.0   
236    501260     2011              Kings XI Punjab   runs          111.0   
1039  1422134     2024        Kolkata Knight Riders   runs          106.0   
55     336038  2007/08             Rajasthan Royals   runs          105.0   

      win_margin  
620          146  
560          144  
0            140  
496          138  
352          130  
706          118  
1009         112  
236          111  
1039         106  
55           105  

Q12:
result
wickets      578
runs         498
tie           14
no result      5
Name: count, dtype: int64

Q13:
Before: object
After : datetime64[ns]

Q14:
        date  year
0 2008-04-18  2008
1 2008-04-19  2008
2 2008-04-19  2008
3 2008-04-20  2008
4 2008-04-20  2008

Q15:
       id                       winner   result  result_margin        win_type
0  335982        Kolkata Knight Riders     runs          140.0     Won by Runs
1  335983          Chennai Super Kings     runs           33.0     Won by Runs
2  335984             Delhi Daredevils  wickets            9.0  Won by Wickets
3  335985  Royal Challengers Bangalore  wickets            5.0  Won by Wickets
4  335986        Kolkata Knight Riders  wickets            5.0  Won by Wickets
5  335987             Rajasthan Royals  wickets            6.0  Won by Wickets
6  335988             Delhi Daredevils  wickets            9.0  Won by Wickets
7  335989          Chennai Super Kings     runs            6.0     Won by Runs
8  335990             Rajasthan Royals  wickets            3.0  Won by Wickets
9  335991              Kings XI Punjab     runs           66.0     Won by Runs

win_type counts:
win_type
Won by Wickets    578
Won by Runs       498
Tie                14
No Result           5
Name: count, dtype: int64

(1095, 23)
       id       date  year  win_margin        win_type
0  335982 2008-04-18  2008         140     Won by Runs
1  335983 2008-04-19  2008          33     Won by Runs
2  335984 2008-04-19  2008           9  Won by Wickets
3  335985 2008-04-20  2008           5  Won by Wickets
4  335986 2008-04-20  2008           5  Won by Wickets
```

## Result

The experiment was executed successfully. All 15 tasks — dataset understanding, cleaning, group-by analysis, filtering, cross-tabulation, aggregation, and data transformations — were completed. The dataset is now clean and enriched with new features (`year`, `win_margin`, `win_type`), revealing that Mumbai Indians are the most successful team, fielding first is the dominant toss choice, and M. Chinnaswamy Stadium, Eden Gardens, and Wankhede Stadium are the top IPL venues.
