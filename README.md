# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The goal of the project is to look through the data and clean it. Then after cleaning
the data we can look at it to see what insights we can learn about the transactions
occuring on this website.

## Process
### First I look through the data for discrepancies and NULL values 
### Using data from other tables I fill in values that would make sense based off the data that is already here
### Now that the data has been cleaned I will query the data to see what relationships between the data there is

## Results

I wanted to take a look at who the visited the site the most and their contributions to the revenue of the site.
The top 10 people who visited the site only visited the site within a range of 4-10 times(i.e the tenth person
on the list visited the site 4 times and the first person on the list visited 10 times). I checked the total
transaction revenue related to these people and noticed that only one person here actually had and transactions at all.
So interestingly enough visiting the page a bunch does not lead to purchases generally. 

As we only have one persons transactions to compare to the rest of the dataset I decided to take a look at that. 
The one person who made transactions(within the top 10) actually also was the person who had the most transaction revenue
across the entire site.

Next I checked to see the average transaction revenue compared to how many times people visit the page. Only people who visited
the page 2, 3 or 6 times had a transaction at all. If a person visited the page 6 times they actually had by far the largest
average transaction revenue but this is definitely due to the person with the largest transaction revenue being in this group
and that this is a small group causing it to skew greatly.

## Challenges 

By far the hardest part was cleaning the data. This dataset has an incredibly large amount of holes and NULL values
within it causing lots of discrepancies and missing data across the tables. This meant careful scouring of the tables
to find the clues that will allow me to fill in some of the data in the table. 

## Future Goals

Due to the time constraints I feel I was unable to do an adequate job of cleaning the data besides a couple of small things.
I would spend way more time cleaning the data and making sure that data was filled in where it should be as well as 
removing redundant data across tables as well as consolidating tables to make the data more coherent.
