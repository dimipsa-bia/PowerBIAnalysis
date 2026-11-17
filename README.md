## PowerBIAnalysis
# Using Power BI to show visuals that relate to an animal charity donations dataset found on Kaggle:  https://www.kaggle.com/datasets/jaderz/synthetic-animal-charity-donations/data

My aim is to use PowerBI to show key visualisations for a dataset relevant to the charity sector. I chose the synthetic animal charity donations dataset from Kaggle, as it aligned to these aims.
I downloaded the dataset from Kaggle in csv format.

In order to manage the project, I created a kanban board using the projects feature in GitHub. This allowed me to outline tasks and items and track what has been completed, as well as bottlenecks in the project.
As can be seen, the first bottleneck is importing the data, which becomes the first task that needs to be completed. <img width="1914" height="763" alt="image" src="https://github.com/user-attachments/assets/a56453b5-b968-4b79-b47d-3fc2a31e173b" />


1. Importing Data into PowerBI
  I selected to satart a new PowerBI project by uploading an Excel worksheet. As the downloaded file was a csv file, I needed to make sure I could see all files in the upload window.
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/347a53fc-03c3-48cc-9e32-2a6812bec25d" />


2. Cleaning Data using PowerQuery
The first step was to check data types; all were correct.
<img width="1726" height="887" alt="image" src="https://github.com/user-attachments/assets/2c085d3d-28a9-49dc-b2b5-27840d2e1bb2" />

I then used the column manager to get an ovwerview of the data quality. the order ID has 1000 distinct values, and 1000 distinct emails, but some names are repeated.
I used the Value Distribution table to find which Name records were repeated to check hese records by filtering the Name column.
By filtering for the two names with the highest count, I can check these records.
<img width="1270" height="715" alt="image" src="https://github.com/user-attachments/assets/1e04b75a-84fb-4e9f-a9a7-d3d7bc1674c1" />
Each record seems to be unique and correct, just multiple people sharing the name. Probably due to this being a synthetic dataset.

