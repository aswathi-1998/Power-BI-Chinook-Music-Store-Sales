# Chinook Music Store Sales Analysis
An analysis of the sales of the fictional music Company chinook. From importing the data from a MySQL database to delivering insights using Power BI

## Data Source

This project uses the [Chinook sample database](https://github.com/lerocha/chinook-database)
by Luis Rocha — a fictional digital media store covering artists, albums,
tracks, invoices, customers and employees.

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

<img width="795" height="490" alt="image" src="https://github.com/user-attachments/assets/3250e904-f62f-4ad5-bfef-c0ab97df9182" />

Note that the major Dimension tables were the Customer, Track and Date Tables, which follows a star schema. Since the playlist track Table could only be merged with the Track table but doing so would create a many to many relationship between the Fact and Dimension Tables, it was converted to a table bridging the Track Table and Playlist Table.
