# Meta-Ads-Performance-Analysis
Facebook/Instagram Add performance analysis and comparison.


# Meta Ads Performance Analysis: Audit First, Analyze Second #
An end-to-end analysis of 400,000 paid advertising events on Facebook and Instagram, using a Dual-Layer Validation workflow (Power BI + MySQL). The project asks two questions: how did the ads perform? and, more importantly, can this data be trusted at all?

## Introduction
This documentation is divided into two sections on the same page:

- Section 1: The Project follows the six phases of the analysis (Ask, Prepare, Process, Analyze, Share, Act), from the business questions to the dashboard and recommendations.

- Section 2: The Evidence explains, in plain language, why the dataset turned out to be synthetic, and what gave it away.


## Table of Contents
### Section 1: The Project
- [Project Overview and Business Task](#project-overview-and-business-task)
- [Data Sourcing and Integrity](#data-sourcing-and-integrity)
- [Tools and Uses](#tools-and-uses)
- [Data Engineering and Data Audit](#data-engineering-and-data-audit)
- [Analysis and Findings](#analysis-and-findings)
- [Powerbi Dashboard](#powerbi-dashboard)
- [Strategic Recommendations](#strategic-recommendations)
### Section 2: The Evidence
- [The Approach](#the-approach)
- [Things That Cannot Happen on a Real Platform](#things-that-cannot-happen-on-a-real-platform)
- [Too Perfect to Be Human](#too-perfect-to-be-human)
- [The Generators Fingerprints](#the-generators-fingerprints)
- [The Verdict](#the-verdict)


## Section 1: The Project
### Project Overview and Business Task
#### Ask Phase
This project analyzes three months of paid advertising data (7 May to 6 August 2025) covering 200 ads, 50 campaigns and 9,841 users across Facebook and Instagram. The goal was to help the marketing team decide where to focus its budget and creative effort.

The analysis was designed around four stakeholders:

- CMO (Chief Marketing Officer): needs to know which platform and ad format deserve more investment.
- Performance Marketing Manager: needs to know which ads and audiences to scale or pause.
- Creative Team: needs to know which ad types earn the most engagement.
- Finance: needs visibility into how the budget is spread across campaigns.
  
The analysis had to answer:

- Does Facebook or Instagram perform better?
- Does a bigger budget bring more reach?
- Which audiences (gender, age, country) respond best?
- Does the best ad format differ by platform?
- Do the ads reach the people they target?
- Does performance change by time of day or day of week?
  
Strategic Note: early in the work, the data showed patterns no real advertising account produces. So I made checking the data itself the main objective: is this genuine platform data or synthetic data? Section 2 answers that question.

### Data Sourcing and Integrity
#### Prepare Phase
The dataset is a publicly available synthetic dataset modelled on the structure of Meta Ads data. It contains four linked tables: ad events (400,000 rows), users (9,841), ads (200) and campaigns (50), with a total campaign budget of $2,535,923.78.

ROCCC Evaluation (Reliable, Original, Comprehensive, Current, Cited):

- Reliable (LOW): the data breaks basic advertising logic (see Section 2).
- Original (LOW): synthetic data, not an export from an ad platform.
- Comprehensive (MEDIUM): well-linked tables, but no revenue and no actual spend, so ROI (Return On Investment) can't be measured.
- Current (MEDIUM): recent, but May and August are only partial months.
- Cited (MEDIUM): publicly available, but how it was generated isn't documented.
  
##### Tools and Uses

- Microsoft Power BI: data import and cleaning in Power Query, a dedicated calendar table, DAX (Data Analysis Expressions) measures for every KPI (Key Performance Indicator), and an interactive dashboard comparing both platforms side by side.
- MySQL (Workbench): an independent rebuild of the whole data model, load validation, and SQL queries that reproduce every dashboard number and test whether the data is real.

### Data Engineering and Data Audit
#### Process Phase
##### Step 1: Importing Safely
Software often guesses what type of data a column holds, and a wrong guess can silently damage it. So every column type was set by hand. User IDs were kept as text, so an ID like 00062 keeps its zeros, and dates were read as day-month-year, so 4 June doesn't silently become 6 April.

##### Step 2: What the Audit Found
346 damaged user IDs: before the data was published, the users file had been opened and saved in a spreadsheet program, which "corrected" some IDs. For example, 012e0 became 1.20E+01 and 00062 became 62. As a result, 20,046 events (5%) no longer match a user. They are labelled "Unknown" instead of being silently dropped.
  
Two empty campaigns: two campaigns hold 152,872 of budget but have no ads,
so the average budget is calculated over the 48 active campaigns (49,646.91).

Partial weeks: the first week has only 5 days of data and the last only 3, so their shorter bars in the weekly chart reflect the calendar, not a drop in activity.
  
##### Step 3: The MySQL Rebuild
The model was rebuilt from scratch in MySQL with exact money values (DECIMAL(10,2)), text-only IDs and dates converted on load. Eight checks confirmed the load was complete and correct, from the row counts to the total budget, accurate to the cent. 

See SQL Queries Here

### Analysis and Findings
#### Analyze Phase
Every result below was produced in Power BI and reproduced independently in MySQL, with identical numbers.

1.	Facebook and Instagram perform the same. Every rate differs by less than half a percentage point. For example, the CTR (Click-Through Rate) is 11.76% on Facebook and 11.86% on Instagram.
2.	Bigger totals come only from more ads. Facebook has 74% more impressions, but only because it runs 127 ads against Instagram's 73. Each ad earns about 1,700 impressions on either platform.
3.	Budget doesn't buy reach. The campaign with the smallest budget ($7,918) gets the most impressions, simply because it has more ads.
4.	No audience responds better. CTR stays between about 11.6% and 12.0% for every gender, age group and country. Female-targeted ads get the most impressions but the lowest CTR, so "more volume" doesn't mean "better performance."
5.	No ad format wins. CTR ranges only from 11.67% to 12.17% across all formats and both platforms.
6.	Targeting has no effect. Ads aimed at women are seen by women exactly as often as ads aimed at everyone (34%).
7.	There is no timing pattern. Activity is the same at 3 AM as at 8 PM, and the same on Sunday as on Monday.
   
Find SQL Analysis and Queries Here


### Powerbi Dashboard
#### Share Phase
Below is a snapshot of the dashboard, with one page per platform.

<img width="875" height="499" alt="image" src="https://github.com/user-attachments/assets/fd882a64-496d-4aa1-9202-c94666336be8" />

<img width="875" height="500" alt="image" src="https://github.com/user-attachments/assets/4df38877-c3e8-4680-b019-f2406828dfc0" />

##### Abbreviations Used on the Dashboard:
- CTR: Click-Through Rate (KPI card and matrix)
- ER: Engagement Rate (matrix header)
- CR: Conversion Rate (matrix header)
- PR: Purchase Rate (matrix header)
- CLKS: Clicks (matrix header)
- IMPR: Impressions (matrix header)
- Avg: Average ("Avg Budget (active)" card)
- Vs: versus, meaning "compared with" ("Vs Instagram" labels)
- K / M: thousands / millions ($49.65K, $2.54M)
  
##### Key Design Features:

- Side-by-side platform comparison: every KPI card shows how the platform compares with the other one (for example, "−0.11% Vs Instagram"), using DAX CALCULATE.
- Dynamic measure switcher: one selector changes every chart between Impressions, Clicks, Engagements and the other counts.
- Honest charts: the hourly chart starts at zero, so small random wobbles don't look like real peaks.
- Native calendar heatmap: a custom matrix calendar shows activity day by day for the selected month.

### Strategic Recommendations
#### Act Phase
#### Executive Summary (Final Conclusion)
The dashboard is fully validated, but the data underneath it is synthetic. In this dataset, no platform, audience, format or time of day performs differently, and budget has no effect at all. The most valuable recommendation is therefore about checking data before trusting it:

1.	Check the Data Before the Dashboard
- The Data: most clicks come from people who never saw the ad, and over half of all events happen outside their campaign's dates.
- The Action: add automatic checks to the reporting process, and stop the report when they fail.
2.	Compare Rates, Not Totals
- The Data: Facebook looks stronger by volume, but both platforms perform the same per ad.
- The Action: judge platforms and audiences by rates such as CTR. Bigger totals usually mean bigger investment, not better results.
3.	Keep IDs Away From Spreadsheets
- The Data: a spreadsheet program damaged 346 user IDs, cutting 5% of events off from their users.
- The Action: never open and save raw ID files in a spreadsheet program, and check ID formats every time data is loaded.

#### Next Steps and Additional Data Required:
- Run the same audit on a genuine ad platform export before analysing it.
- Add revenue and actual spend, so that cost per click and return on ad spend can be measured.


## Section 2: The Evidence
### The Approach
I treated the data the way a fair court treats a defendant: assume it's genuine, then look for evidence strong enough to prove otherwise. For each test, I first asked what real advertising data would have to look like, then checked whether this data does. 
See the SQL Queries Behind Each Test Here

#### Things That Cannot Happen on a Real Platform
1. Clicks From People Who Never Saw the Ad
   
To click an ad, you first have to see it, just as you can't review a restaurant you never walked into. Yet 84% of all clicks came from people who were never shown that ad, and 99% of purchases came from people who never clicked it. On a real platform, both numbers would be close to zero.

2. Ads Running When Their Campaign Wasn't

An ad can only appear while its campaign is running, just as sale prices only exist during the sale. A receipt showing a sale price two weeks after the sale ended would be proof the receipt is fake. Here, 56% of all events happened before their campaign started or after it ended. The very first event in the data is a "like" on 26 July, for a campaign that ended on 13 July.

3. Campaigns With Money but No Ads

No real advertiser commits $152,872 to two running campaigns and then never creates a single ad for them.

#### Too Perfect to Be Human

4. Targeting That Does Nothing

Targeting is the reason advertisers pay for these platforms: an ad set to "show to women" should be seen mostly by women. Here, ads aimed at women were seen by women 34% of the time, exactly the same as ads aimed at everyone. The setting changes nothing.

5. An Audience That Never Sleeps

Real people sleep, work and relax at different times, so real ad activity drops at night and changes at the weekend. Here, every hour of the day gets the same share of activity (about 4.2%), and every day of the week gets the same share (about 14.3%). 3 AM is as busy as 8 PM, and Sunday looks exactly like Monday. That's what random timestamps produce, not human behaviour.

6. Money That Buys Nothing
   
In real advertising, more budget buys more reach. Here, two campaigns with almost the same budget ($71,000) received 1,686 and 13,425 impressions, an eightfold difference, simply because one had 1 ad and the other 8. The amount of money never makes a difference.

7. A Mix That Reads Like a Recipe

If a café recorded every order for three months, the shares would be uneven, such as coffee 41.7% and tea 23.4%, because every customer chooses for their own reasons. A report showing exactly 40%, 30%, 20% and 10% would look made up. This data shows impressions 85%, clicks 10%, likes 3%, comments 1%, purchases 0.5% and shares 0.5%: six round numbers that add up to exactly 100. That's a recipe a programmer typed in, not the result of real behaviour.
#### The Generator's Fingerprints
8. Ages That Pile Up at the Limits

In a real group of people, the number of people changes gradually from one age to the next. Imagine a shop that labels every item cheaper than €16 as "€16": you'd find a suspicious pile of items at exactly €16. The same happens here. Age 16 has 615 users while age 17 has only 265, and age 65 has more users than any age from 60 to 64. A program drew random ages and pushed every age outside 16–65 onto the nearest limit.

9. A Window Measured to the Minute

The first event happened at 14:11:57 on 7 May 2025, and the last at 14:11:30 on 6 August 2025: exactly 91 days, to within half a minute. Real data starts at a natural point, like the beginning of a day or a campaign. This window was counted back from the moment a script was run.

10. Invented Places and Impossible Results
- Users in Japan live in "Port Charlesmouth", and users in India in "West William": invented English-style names, typical of fake-data tools.
- Campaigns named "Winter" run through the summer.
- The overall CTR is 11.8%, while social media ads are commonly benchmarked at around 1–2%.
  
#### The Verdict

The dataset is synthetic, beyond reasonable doubt. It breaks the basic rules of how an ad platform works, it shows none of the rhythms of real human behaviour, and it carries clear fingerprints of the program that generated it. The recipe can even be reconstructed: for each of 400,000 events, pick a random ad, a random user and a random moment in the last 91 days, then roll a weighted dice for the type of event.

#### Analyst's Final Note:

A dashboard can be technically flawless and still describe nothing real. Every number in this project was validated twice, in Power BI and in MySQL, and every number agrees. That agreement proves the calculations are right, but not that the data is real. Asking that second question is what protects a business from confident decisions built on invented data, and it's why this project audits first and analyzes second.

