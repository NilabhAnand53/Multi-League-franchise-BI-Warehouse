# Global Cricket Business Intelligence Warehouse

A Power BI project analyzing commercial performance for 50 franchises across 7 T20 cricket leagues: revenue, sponsorship, broadcast, attendance, social media, and merchandise, built into one connected data model.

## Numbers, up front

Across the warehouse: about $23.4M in revenue, $30.4M in sponsorship, $27.7M in broadcast value, and $18.2M in merchandise, for roughly $99.8M in combined commercial value. Attendance totals 78.6M+ across all matches tracked, and the 50 franchises together carry over 1.55 billion social media followers.

Franchise-level and league-level insights, competitive comparisons, and recommendations are still being written up. Everything below this is finished; that part is coming in the next few days (see Project Status).

## Scope

| | |
|---|---|
| Leagues | IPL, ILT20, CPL, BBL, The Hundred, WPL, SA20 |
| Franchises | 50 |
| Stadiums | 42 |
| League-seasons | 57 |
| Fact tables | 6 (400 rows each): Revenue, Sponsorship, Broadcast, Attendance, Social Media, Merchandise |
| Dimension tables | 4: League, Franchise, Stadium, League_Season |
| Tools | Power BI, Power Query, DAX |

## Data model

Star schema. Four dimensions feed six fact tables, all at Franchise x League_Season grain.

| From | To | Notes |
|---|---|---|
| League | Franchise | active |
| League | League_Season | inactive (see below) |
| League_Season | all 6 fact tables | active |
| Franchise | all 6 fact tables | active |
| Franchise | Stadium | active, some franchises unmatched |

League to League_Season is turned off deliberately. Both League_Season and Franchise link back to League, and Power BI won't allow two active paths between the same tables. Franchise's path stays active since every fact table already runs through it. League_Season's link to League is still available on demand through USERELATIONSHIP() when a specific measure needs it.

## Cleaning and modeling decisions

The fact tables came back clean on inspection: no missing values, no duplicate keys, and full referential integrity against Franchise and League_Season, checked directly rather than assumed.

The dimension side needed real work. An early Owner table had two duplicate rows, four missing records, and a naming mismatch ("Hundred" vs. "The Hundred") that broke joins silently. Beyond the cleanup, Owner_ID itself wasn't unique per row, because the same owner can hold franchises in more than one league. The right fix is a composite key of Owner_ID and League_ID. For this version, the table was dropped instead, to keep the model simpler while that fix gets built properly. Bringing it back as a proper bridge table is planned for the next version.

Every ID column across all 11 tables is typed as text, even the numeric-looking ones, so nothing gets summed by accident. Currency fields are fixed decimal. Text columns went through a trim and clean pass in Power Query.

## Report pages

| Page | Focus |
|---|---|
| Revenue | Composition by channel, revenue per attendee, franchise ranking |
| Sponsorship | Title, kit, digital, and local sponsorship split by team and league |
| Broadcast | Domestic vs. international vs. digital streaming |
| Merchandise | Jersey, online, retail, accessories, merchandise per follower |
| Attendance | Stadium utilization, attendance volatility, league drill-down |
| Social Media | Platform split across Instagram, X, Facebook, YouTube |
| Cross-Fact Overview | All six fact tables pulled into a single view |

Chart types vary deliberately across pages: area charts, scatter plots, gauges, a treemap, a decomposition tree, and waterfall charts, rather than the same bar chart repeated seven times.

## Technical notes

Over 45 DAX measures across the six fact tables: base totals, percent-of-total breakdowns, RANKX-based leaderboards, and a few cross-fact ratios like revenue per attendee and merchandise per follower. One relationship ambiguity got resolved with a composite key plus USERELATIONSHIP(). Ranking cards use HASONEVALUE so they show "select a franchise" instead of a meaningless number when nothing specific is selected.

## Repository layout

```
Multi_League_Franchise_BI_Warehouse.pbix
data/            source Excel files, 4 dimension + 6 fact tables
screenshots/     dashboard page previews (coming shortly)
README.md
```

## Project status

Done: data model, cleaning, all relationships, DAX measures, all 7 report pages.

Coming this week: the actual business insights, franchise and league comparisons, and recommendations that this model is built to support.
