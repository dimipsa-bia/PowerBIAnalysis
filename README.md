## PowerBIAnalysis
# Using Power BI to show visuals that relate to an animal charity donations dataset found on Kaggle:  https://www.kaggle.com/datasets/jaderz/synthetic-animal-charity-donations/data

My aim is to use PowerBI to show key visualisations for a dataset relevant to the charity sector. I chose the synthetic animal charity donations dataset from Kaggle, as it aligned to these aims.
I downloaded the dataset from Kaggle in csv format.

In order to manage the project, I created a kanban board using the projects feature in GitHub. This allowed me to outline tasks and items and track what has been completed, as well as bottlenecks in the project.
As can be seen, the first bottleneck is importing the data, which becomes the first task that needs to be completed. <img width="1914" height="763" alt="image" src="https://github.com/user-attachments/assets/a56453b5-b968-4b79-b47d-3fc2a31e173b" />

______________________________________________________________________________________________________________________________________________________________________________________________________________________________
**1. Importing Data into PowerBI**
  I selected to satart a new PowerBI project by uploading an Excel worksheet. As the downloaded file was a csv file, I needed to make sure I could see all files in the upload window.
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/347a53fc-03c3-48cc-9e32-2a6812bec25d" />


**2. Cleaning Data using PowerQuery**
The first step was to check data types; all were correct.
<img width="1726" height="887" alt="image" src="https://github.com/user-attachments/assets/2c085d3d-28a9-49dc-b2b5-27840d2e1bb2" />

I then used the column manager to get an ovwerview of the data quality. the order ID has 1000 distinct values, and 1000 distinct emails, but some names are repeated.
I used the Value Distribution table to find which Name records were repeated to check hese records by filtering the Name column.
By filtering for the two names with the highest count, I can check these records.
<img width="1270" height="715" alt="image" src="https://github.com/user-attachments/assets/1e04b75a-84fb-4e9f-a9a7-d3d7bc1674c1" />
Each record seems to be unique and correct, just multiple people sharing the name. Probably due to this being a synthetic dataset.

Review of Project progress:
<img width="1794" height="358" alt="image" src="https://github.com/user-attachments/assets/939ee0eb-56ff-4e41-9b09-0f47738a3514" />

**3. Formulate Analysis Question**
Clicking 'Close & Apply', I now have the data with the table columns being viewable in the data panel.
I want to formulate a larger question which can be researched through two smaller tasks.

**Important Note**: To aid with the analysis and design, I am applying this dataset as if it is from the real conservation charity 'Northumbiran Hedgehog Rescue Fund'. More information on this non-profit can be found at this link:  https://hedgehogs-northumbria.org.uk/
I therefore created a theme, using brand basics, such as the logo and the minimal colour scheme: mainly white, black and some natural greens.
<img width="1361" height="902" alt="image" src="https://github.com/user-attachments/assets/76067125-6e01-4146-9c81-1533ec7864d7" />

The main question I want to answer is: *Which referral channel is the most effective?*
To explore this, I will create a report on donations by referral channel, and one on referral channels by demographics.


**4. Create DAX Measures Useful for Questions**
In order to create the "Average Donation by Channel" column chart, I used a wuick measure function for Average according to refferal channel.
```
Avg Donation = AVERAGE(animal_charity_donation_records[donation_amount]) 
```
Then, to create a 2026 Forecast, I needed to complete the 2025 data, and make a linear forecast from 2024. To do this, I calculated the Total Donations of 2024:
```
2024_Total = 
CALCULATE(
	SUM('animal_charity_donation_records'[donation_amount]),
	'animal_charity_donation_records'[donation_date].[Year]
		IN { 2024 }
)
```
Then as the records finished on 17th June 2025, (46% of the way through the year) I forecasted the 2025 records for the entire year from this sample:
```2025TotalForecast = CALCULATE(
	SUM('animal_charity_donation_records'[donation_amount]),
	'animal_charity_donation_records'[donation_date].[Year]
		IN { 2025 }
) / 0.46
```
And finally, I found the growth percentage of 2025 on 2024, and applied this to the 2025 forecast to create a linear forecast for the end of 2026:
```
2026 Forecast = animal_charity_donation_records[2025TotalForecast]/animal_charity_donation_records[2024_Total] * animal_charity_donation_records[2025TotalForecast]
```
The demographic page of the report required no DAX measures.

For the final dashboard page, I wanted to have the ability to select and visualise the same charts using a slicer for the left side and a separate slicer for the right side, to quickly compare the performance of two referral channels.
I created one table that was based on the referral channel from Slicer 1, and another for SLicer 2 *For all of the following DAX functions, change out any reference to Slicer 1 for Slicer 2 and keep all else the same*
```
Referral Slicer 1 = DISTINCT(animal_charity_donation_records[referral_channel])
```
```
Slicer 1 Total Donations = 
VAR SelectedChannel =
    SELECTEDVALUE(
        'Referral Slicer 1'[referral_channel]
    )
RETURN
    IF(
        NOT ISBLANK(SelectedChannel),
        CALCULATE(
            SUM(animal_charity_donation_records[donation_amount]),
            REMOVEFILTERS(animal_charity_donation_records[referral_channel]),
            animal_charity_donation_records[referral_channel] = SelectedChannel
        )
    )
```
```
Slicer 1 Count = 
VAR SelectedChannel =
    SELECTEDVALUE(
        'Referral Slicer 1'[referral_channel]
    )
RETURN
    IF(
        NOT ISBLANK(SelectedChannel),
        CALCULATE(
            COUNT(
                animal_charity_donation_records[donor_id]
            ),
            REMOVEFILTERS(
                animal_charity_donation_records[referral_channel]
            ),
            animal_charity_donation_records[referral_channel] = SelectedChannel
        ))
```
```
S1 AVG Donation = 'Referral Slicer 1'[Slicer 1 Total Donations]/[Slicer 1 Count]
```
```
S1 2024 = 
VAR SelectedChannel =
    SELECTEDVALUE(
        'Referral Slicer 1'[referral_channel]
    )
RETURN
    CALCULATE(
        SUM('animal_charity_donation_records'[donation_amount]),
        'animal_charity_donation_records'[donation_date].[Year] = 2024,
        REMOVEFILTERS(
            'animal_charity_donation_records'[referral_channel]
        ),
        'animal_charity_donation_records'[referral_channel] = SelectedChannel
    )
```
```
S1 2025 = 
VAR SelectedChannel =
    SELECTEDVALUE(
        'Referral Slicer 1'[referral_channel]
    )
RETURN
    CALCULATE(
        SUM('animal_charity_donation_records'[donation_amount]),
        'animal_charity_donation_records'[donation_date].[Year] = 2025,
        REMOVEFILTERS(
            'animal_charity_donation_records'[referral_channel]
        ),
        'animal_charity_donation_records'[referral_channel] = SelectedChannel
    ) / 0.46
```
```
S1 2026 = [S1 2025] / [S1 2024] * [S1 2025]
```

**5. Create Reports Useful for Tasks**
<img width="1348" height="757" alt="image" src="https://github.com/user-attachments/assets/babb04e9-e4e4-45d1-a531-9c7f720c8af4" />
For the first page, I opted to start with a pie chart, to quickly see the distribution of donations across referral channels.
I then created a bar chart which comapred the average donation by referral channel.
I used the forecast DAX funtions above to create 2026 Forecast KPI cards, showing percentage increase or decrease from a target value (2024 Donations).
I completed the page by having slicer functions to look at the data by year and by donation type, to look at Monthly paymetns or One-Off payments respectively.

<img width="1342" height="755" alt="image" src="https://github.com/user-attachments/assets/b756f1a9-70d5-47d1-8358-c443a2c614bc" />
For the second page, I opted to start with a scatter chart, looking at total donations by country, with avergae donation selected as size. When comparing all 6 referral channels, this can be hard to guage, so I think it is used best when looking at 2 or 3 in comparison, using the slicer (selecting multiple with Ctrl).
I added a Total Donation by Age Group column chart to visualise the distribution of donations across age group.


**6. Create Dashboard For Question**
<img width="1342" height="755" alt="image" src="https://github.com/user-attachments/assets/c89aee93-20b1-439e-b36d-16ba21ad4a8e" />
In order to quickly see the key performance of a given referral channel in comparison to another, I created a left/right divide by adding a central line. In each half I included some key data from previous reports, this time separated individually by referral channel by using two new tables (one for each slicer) to hold data.
I included the Total Donation and Average DOnation by referral channel and country scatter chart from page two as well as an Avergae Donation by Age Group column chart.
At a quick glance, the top of the dashboard provides key indicators through cards and KPI cards. With total count of donations from the referral channel, the average donation for the channel, and the 2026 Forecast vs 2024 Outcome KPI card.

**Final Project Kanban**
<img width="1086" height="634" alt="image" src="https://github.com/user-attachments/assets/1ec5a8f8-c512-4964-ad5c-229f8f824649" />

