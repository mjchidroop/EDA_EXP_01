# Experiment 1: EDA on IPL Dataset

## Aim
To perform Exploratory Data Analysis (EDA) on the IPL matches dataset and derive insights about matches per season, winning teams, toss decisions, and top venues.

## Algorithm / Procedure

**1. Import Libraries**
- Import pandas for data handling.
- Import matplotlib and seaborn for visualization.

**2. Load Dataset**
- Use `pd.read_csv()` to load the IPL matches dataset.
- Check dataset shape using `.shape`.
- View first 5 rows using `.head()`.

**3. Matches per Season (Univariate Analysis)**
- Group data by season and count matches.
- Plot a bar chart to visualize growth/decline in matches.

**4. Top Winning Teams (Univariate Analysis)**
- Use `value_counts()` on the winner column.
- Plot top 5 winning teams in a bar chart.

**5. Toss Decisions (Univariate Analysis)**
- Count toss decision preferences (bat vs field).
- Plot results using a bar chart.

**6. Top Venues (Univariate Analysis)**
- Count matches per venue.
- Display top 5 venues with a horizontal bar chart.

**7. Draw Insights**
- Observe patterns in toss decisions.
- Identify teams with consistent winning trends.

## Program
Import libraries:
```py
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
sns.set_style("whitegrid")
plt.rcParams['figure.figsize'] = (10, 5)
plt.rcParams['font.size'] = 11
print("Libraries imported successfully!")
```

Load Dataset:
```py
df = pd.read_csv('matches.csv')
print("Dataset Shape:", df.shape)
print("\nFirst 5 rows:")
df.head()
```

Basic Data Info
```py
print("Column Names:\n", df.columns.tolist())
print("\nData Types:\n")
print(df.dtypes)
print("\nMissing Values:\n")
print(df.isnull().sum())
```

Matches per Season (Univariate Analysis)
```py
matches_per_season = df['season'].value_counts().sort_index()
print("Matches per Season:\n")
print(matches_per_season)

plt.figure(figsize=(12, 5))
sns.barplot(x=matches_per_season.index, y=matches_per_season.values, palette='viridis')
plt.title('Number of Matches Played per Season', fontsize=14, fontweight='bold')
plt.xlabel('Season')
plt.ylabel('Number of Matches')
plt.xticks(rotation=45)
for i, v in enumerate(matches_per_season.values):
    plt.text(i, v + 0.5, str(v), ha='center', fontweight='bold')
plt.tight_layout()
plt.show()
```

Top Winning Teams (Univariate Analysis)
```py
top_teams = df['winner'].value_counts().head(5)
print("Top 5 Winning Teams:\n")
print(top_teams)

plt.figure(figsize=(10, 5))
sns.barplot(x=top_teams.index, y=top_teams.values, palette='magma')
plt.title('Top 5 Winning Teams in IPL', fontsize=14, fontweight='bold')
plt.xlabel('Team')
plt.ylabel('Number of Wins')
plt.xticks(rotation=25)
for i, v in enumerate(top_teams.values):
    plt.text(i, v + 1, str(v), ha='center', fontweight='bold')
plt.tight_layout()
plt.show()
```
Toss Decisions (Univariate Analysis)
```py
toss_decision = df['toss_decision'].value_counts()
print("Toss Decision Counts:\n")
print(toss_decision)

plt.figure(figsize=(7, 5))
sns.barplot(x=toss_decision.index, y=toss_decision.values, palette='coolwarm')
plt.title('Toss Decision Preferences (Bat vs Field)', fontsize=14, fontweight='bold')
plt.xlabel('Toss Decision')
plt.ylabel('Count')
for i, v in enumerate(toss_decision.values):
    plt.text(i, v + 2, str(v), ha='center', fontweight='bold')
plt.tight_layout()
plt.show()
```

Top Venues (Univariate Analysis)
```py
top_venues = df['venue'].value_counts().head(5)
print("Top 5 Venues with Maximum Matches:\n")
print(top_venues)

plt.figure(figsize=(10, 5))
sns.barplot(x=top_venues.values, y=top_venues.index, palette='Blues_d')
plt.title('Top 5 Venues Hosting Maximum IPL Matches', fontsize=14, fontweight='bold')
plt.xlabel('Number of Matches')
plt.ylabel('Venue')
for i, v in enumerate(top_venues.values):
    plt.text(v + 0.5, i, str(v), va='center', fontweight='bold')
plt.tight_layout()
plt.show()
```
Toss Winner vs Match Winner Analysis
```py
toss_win_match_win = (df['toss_winner'] == df['winner']).value_counts()
print("Toss Winner also won the Match?")
print(toss_win_match_win)

plt.figure(figsize=(6, 4))
sns.barplot(x=['No', 'Yes'], y=toss_win_match_win.values, palette='Set2')
plt.title('Toss Winner vs Match Winner', fontsize=14, fontweight='bold')
plt.ylabel('Number of Matches')
for i, v in enumerate(toss_win_match_win.values):
    plt.text(i, v + 5, str(v), ha='center', fontweight='bold')
plt.tight_layout()
plt.show()
```

## Output
**Dataset Shape:** (1095, 20)

First Five Rows:
<img width="1599" height="236" alt="image" src="https://github.com/user-attachments/assets/d8d0cf35-b617-43d4-918f-c0143a1ca6cb" />



Number of matches played per season: 

<img width="1187" height="487" alt="image" src="https://github.com/user-attachments/assets/e52362a0-0081-4542-ada2-3385e77ef1d7" />

Top 5 winning teams in IPL: 

<img width="987" height="486" alt="image" src="https://github.com/user-attachments/assets/2e808623-e8c7-4fa4-9629-15725eadbaa6" />

Toss Decision Preferences (Bat vs Field) : 

<img width="686" height="487" alt="image" src="https://github.com/user-attachments/assets/2ef5c615-4fbb-4555-814c-77ff4929d1b4" />

Top 5 Venues hosting IPL matches: 

<img width="988" height="487" alt="image" src="https://github.com/user-attachments/assets/1780b22b-9f4a-4cf2-b4df-2e83f80393de" />

Toss winner vs match winner: 

<img width="586" height="387" alt="image" src="https://github.com/user-attachments/assets/6d667f91-3303-4622-bf7f-f3e653a68df1" />

## Key Insights :
- Matches per Season: The number of matches grew steadily across seasons, reflecting the expansion of the IPL. 2013 and 2012 had the highest match counts (due to additional playoff/league matches).

- Top Winning Teams: Mumbai Indians (MI) and Chennai Super Kings (CSK) are the most successful teams in IPL history, followed closely by Kolkata Knight Riders (KKR) and Royal Challengers Bangalore (RCB).

- Toss Decisions: Most teams prefer to field first after winning the toss, as chasing has proven advantageous in T20 cricket.

- Top Venues: Wankhede Stadium (Mumbai) and Eden Gardens (Kolkata) host the maximum number of IPL matches, followed by M. Chinnaswamy Stadium and Feroz Shah Kotla.

- Toss Impact: Winning the toss has a moderate correlation with winning the match — but not a decisive one, showing that team performance matters more than toss luck.

## Result

This experiment is executed successfully. The EDA revealed that Mumbai Indians are the most successful team, teams prefer to field first after winning the toss, and M. Chinnaswamy Stadium, Eden Gardens, and Wankhede Stadium hosted the maximum number of IPL matches.
