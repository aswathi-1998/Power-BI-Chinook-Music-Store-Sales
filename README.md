# Chinook Music Store Sales Analysis
An analysis of the sales of the fictional music Company chinook. From importing the data from a MySQL database to delivering insights using Power BI

## Data Source

This project uses the [Chinook sample database](https://github.com/lerocha/chinook-database)
by Luis Rocha — a fictional digital media store covering artists, albums,
tracks, invoices, customers and employees; from 2021 to 2024.

- Author: Luis Rocha ([@lerocha](https://github.com/lerocha))
- License: MIT — Copyright (c) 2008–2017 Luis Rocha
- Version used: [MySQL script, release vX.X](https://github.com/lerocha/chinook-database/releases)

The data is fictional. Media metadata was derived from an iTunes library;
customer, employee and sales records are synthetic. No conclusions here
reflect any real business.

##  ER Diagram
The original ER diagram is as follows:

<img width="957" height="964" alt="ER diagram" src="https://github.com/user-attachments/assets/24616aff-1846-42e6-9782-44de70f13553" />

Since Power BI Engine is optimized for a star schema, using Power Query, the tables have been flattened and condensed to a star schema. For better time analysis, a dedicated date fact table was also added.

## To Ingest Data
Import Mode was used to ingest the data with the source being MySQL database and the server used was ```localhost:3360```

## Data Model
<img width="717" height="433" alt="image" src="https://github.com/user-attachments/assets/072baffd-8ad9-4d49-a95f-a2d2c5a7d252" />

Note that the major Dimension tables were the Customer, Track and Date Tables, which follows a star schema. Since the playlist track Table could only be merged with the Track table but doing so would create a many to many relationship between the Fact and Dimension Tables, it was converted to a table bridging the Track Table and Playlist Table.

The Data model in Power Query was created as follows:
1. Since the foreign key constraint was already present, merging queries was not necessarily required since Power Query Automatically handled
<img width="795" height="490" alt="image" src="https://github.com/user-attachments/assets/06ce6a37-b884-4c1f-b532-3bfd7dcce85e" />

2. The data was condensed in the following manner. ```Invoice Line``` table was considered the fact table, with appropriate columns from ```Invoice``` table chosen.

3. ```Track``` table was considered as the ```DimTrack``` table with the appropriate columns being taken from the ```Genre```, ```Album```,```Mediatype``` and  ```Artist``` tables.

4. ```Customer ``` table was considered as the ```DimCustomer``` table and appropriate columns from ```Employee``` table was expanded.

5. In all these tables unwanted columns were dropped

6. Since merging ```Playlist``` and ```PlaylistTrack``` with the track table would create unwanted many-to-many relationship, it was avoided and kept as such. Since different playlists had same name, a helper column was created, identifying Id and Track 

7. A dedicated date table called ```DimDate``` was added :
   ```
   DimDate = 
      ADDCOLUMNS(
          CALENDAR(
              DATE(YEAR(MIN(FactSales[InvoiceDate])), 1, 1),
              DATE(YEAR(MAX(FactSales[InvoiceDate])), 12, 31)
          ),
          "Year", YEAR([Date]),
          "Month", FORMAT([Date], "MMM"),
          "MonthNo", MONTH([Date]),
          "Quarter", "Q" & QUARTER([Date]),
          "YearMonth", FORMAT([Date], "YYYY-MM")
      )```
## Data Analysis and Insights

### The Report

Wire Framimg was done and appropriate bacground was created in Power Point. Car was given to create an accessible theme (Geneated with the help of Claude Opus). Buttons were used as page navigators.

<table>
  <tr>
    <td width="50%"><img width="100%" alt="Sales Analysis" src="https://github.com/user-attachments/assets/718beb0c-5b42-4403-8967-f3b6814e0d46" /></td>
    <td width="50%"><img width="100%" alt="PVM Analysis" src="https://github.com/user-attachments/assets/4749c6d1-32f5-4911-9488-7f6c80732a6c" /></td>
  </tr>
  <tr>
    <td width="50%"><img width="100%" alt="Track Analysis" src="https://github.com/user-attachments/assets/e4999616-adb1-4137-8461-08cbcc26a514" /></td>
    <td width="50%"><img width="100%" alt="Customer Analysis" src="https://github.com/user-attachments/assets/834b429f-8e23-4761-bdb9-154b1a64b14a" /></td>
  </tr>
  <tr>
    <td width="50%"><img width="100%" alt="Individual Customer Analysis" src="https://github.com/user-attachments/assets/4f7a8c9a-3543-453d-9caf-885be5001531" /></td>
    <td width="50%"></td>
  </tr>
</table>

The Data Analysis was split into 3.
1. Identify the pattern of Sales Of the Company
2. Identify which are the most in demand tracks/ artists/ albums.
3. Understand the customer preferences.

To facilitate data visualization, few calculated columns like Customer Name (Since Customer First Name and Last Name were given separate), Album Label and Track Label were created (Since some artists had common album/Track names)

### DAX

Measures were created to facilitate analysis. Some of these including
1. ```Sales``` and ```Quantity``` measure, calculating the Revenue received (Price times Units Sold) and Quantity Sold
2. Time Intelligence was applied to calculate value of corresponding metrics in the previous year.
3. Price Effect, Volume Effect and Mix Effect were calculated to do a PVM analysis.
4. A ```TopNSales``` Metric was calculated to identify the top selling Albums/ Artists/ Tracks. The same metric was modified within itself to identify the top Albums/Artist/Tracks in terms of quantity purchased as well as invoice coint (Number of Orders having that particular Album/Artist/Track). To facilitate this dynamic selection, Parameters were used, one for the measures and one for parameters

### Insights
**For Sales**
1.   For each month, the quantity sold remained stagnant at 38 while the number of invoices remained stagnant at 7, and only few months (Jan 2021,Sep 2022, Nov 2023), were there any changes (36,27,and 24 quantity sold respectively and invoice numbers were 6 for all 3 months). Since by inspection, the audio files were priced at 0.99 and video files at 1.99, sales although most of the time remained constant at $37.62, positive fluctuations were present in the early quarter of 2022, since in 2021, only 454 audio and no video files were sold but in 2022, 31 video files had been sold, despite the fall in audio files to 424. The fall in sales in 2023 could be attributed to the poor increase in video file sales (only 32) accompanied by a fall in audio track sales (only 410) along with the highest number of unsold tracks.
  > The positive sales in the early quarters of 2023 were due to the fact that more video files were sold, while in the later quarter, sales tanked due to the lesser number of tracks sold in november

2. Latin, Metal and Rock were the top placed genres across quantity sold, sales and number of invoices containing them, with Alternative Punk and Jazz joining in the quantity and invoice number category while TV shows were part of the Top 5 in the sales category.

3. USA and Canada were leading countries where orders were placed, sales generated and orders placed while Brazil, France and Germany kept fluctuating in and out. It can be noted that while Brazil Contributes higher in terms of orders placed and quantity of tracks bought, its sales contribution is a little less (not too different), indicating that it orders more audio files as compared to video.

4. From the PVM analysis, Volume effect showed a greater contribution to change in sales. Price effect was almost zero, which is understandable, since no product had a price change.

**For Tracks**
1. Across all 4 years,  1519 tracks remained unsold.
 
2. Across all  4 years,  **1519** tracks were never sold. Individual analysis for each year found that nearly 3000 tracks remained unsold every time, i.e. what sells one year is not sold again another year

3. The pareto principle was analyzed, understanding whether music belonging to the top 20% of artists lead to 80% if sales, and was observed that the case was that individually across all 4 years, nearly 20-30% of artists were responsible for 80% of sales. In fact, music belonging nearly 60% of artists lead to more than 95% of sales. This means that the company is storing a lot of unnecessary tracks

4. There were many playlists created, but two playlists titles audiobooks (ids 6 , 4), and two playlists titled movies (ids 2, 7) had no tracks associated with them

**For customers**
1. Most customers placed 7 orders through the 4 years except one who had only 6 orders. Each customer purchased 38 track, except for the one who purchased only 36.
2. Of the 59 customers, 46 made their first purchase in 2021 while the remaining 13 made it in 2022; the rest of the years, no new customers were added
3. Each individual customer was analyzed based on their favorite genre, favorite artist and average days between purchase with the help of a drillthrough page.

### Suggestions
1. There is a need to attract more customers.
2. Unsold tracks may no longer be stored
3. Since there is no date given indicating when the track was added, conclusions on whether new tracks are to be inserted and old tracks are to be removed cannot be made.
4. Incentivize people to buy more video tracks
5. Remove unnecessary playlists if there are no tracks in it. Also merge same named playlists together

## Limitations and Future Scope
1. Since the data set was artificial, real world implications could not be properly analyzed, but it was a good exercise to practice DAX as well as Power BI aspects like Parameters and Drill Through Pages

2. Future scope would be to use this in a real world sales projects

## Key learnings
1. How to connect a SQL database in Power BI
2. Data cleaning using Power Query
3. Learning to keep only required columns
4. DAX prractise
