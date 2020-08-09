# PyCity School Challenge

## Overview of Project

Our primary customer for this project is Maria, the Chief Data Scientist for a local school district.  Maria is conducting a study on standardized tests within her district and need to identify trends in order to support decision-making on budget and district priorities.  She wants us to conduct specific analysis and calculate metrics using these data sets to identify patterns.  This analysis and metrics include the following:
- Overall Summary for the District: *Total Schools, Total Students, Total Budget, Average Math Score, Average Reading Score, % of Students who Passed Math, % of Students who Passed Reading, Overall % of Students who Passed Both*
- Summary of Performance for Each School: *School Type, Total Number of Students, Total School Budget, School Budget per Student, Average Math Score, Average Reading Score, % of Students who Passed Math, % of Students who Passed Reading, Overall % of Students who Passed Both*
- Summary of Scores in Math by Grade for Each School
- Summary of Scores in Reading by Grade for Each School
- Summary of Performance for Each Student Spending Bin: *Average Math Score, Average Reading Score, % of Students who Passed Math, % of Students who Passed Reading, Overall % of Students who Passed Both*
- Summary of Performance for Each School Size Bin: *Average Math Score, Average Reading Score, % of Students who Passed Math, % of Students who Passed Reading, Overall % of Students who Passed Both*
- Summary of Performance for Each School Type:  *Average Math Score, Average Reading Score, % of Students who Passed Math, % of Students who Passed Reading, Overall % of Students who Passed Both*

She has asked us to provide analysis on math and reading scores from standardized tests based on information about each school within the district using two sets of data, [reading and math scores for all students](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/students_complete.csv) and [information on each of the schools](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/schools_complete.csv).  Each data set provided key fields to compute the metrics Maria needed for her analysis, as seen below.  To facilitate this calculation, we merged the school data into the student data on the field school_name using the code below.  

The student score data contains the following information:
- Student Name
- Gender
- Grade
- Name of the School they Attend
- Reading Score
- Math Score	


The school data contains the following information:
- School Name
- Type of School (District or Charter)
- Number of Students at the School
- Budget for the School

```
# Merging the two main data sets into one
student_data_complete_df = pd.merge(student_data_df, school_data_df, on=["school_name"])
```

Following initial analysis, Maria discovered errors in the Thomas High School 9th grade tests and needed to replace the original scores with a value of NaN.  Using the code below to update the Math and Reading scores for this subset, we then re-calculated the summaries and metrics Maria needed for her analysis.  The report results below provide comparison between our original analysis and the updated analysis with the replaced values.  

```
# Step 1. Import numpy as np.
import numpy as np

# Step 2. Use the loc method on the student_data_df to select all the reading scores from the 9th grade at Thomas High School and replace them with NaN.
student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th") & (student_data_df["reading_score"] >0), "reading_score"] = np.nan

# Step 3. Refactor the code in Step 2 to replace the math scores with NaN.
student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th") & (student_data_df["math_score"] >0), "math_score"] = np.nan
```

To identify the number of students and records this impacted, and also to help our comparative analysis, we used the following code.  This code gives us the total number of students impacted, and then the total number of records that are now NaN.  The output of these is all 461, which confirms we successfully updated the data to reflect the errors. 

```
# Identify how many students are in the 9th grade at Thomas High School
student_data_df_remove = student_data_df[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th")]
len(student_data_df_remove)

# Check that the number of reading entries w/ NaN is equal to the number of entries in student_data_df_remove
student_data_df["reading_score"].isnull().sum()

# Check that the number of math entries w/ NaN is equal to the number of entries in student_data_df_remove
student_data_df["math_score"].isnull().sum()
```

### Purpose  

## Results

Overall, the updated records to nullify Thomas High School’s 9th Grade scores had marginal impact on the district in general, but did have an impact on the school’s relative performance in terms of overall passing percentage as well as per capital spending, size, and type of school.  Specifics for each of the changes are listed below.  Of note, due to the formatting for some of the results, the impact is not discernible.  We are identifying these as no significant change, although there is likely a small change.


**QUESTION: How is the district summary affected**

Using the merged data set we calculated the necessary metrics needed for the overall district summary, as displayed below.  Of note, when calculating the number of total students, we cannot use the math or reading scores for a .count function, as they are null and will skew the results.  As such, we utilized the student ID column instead.   

```
# Determine the number of total students 
student_count = student_data_complete_df[“Student “ID].count()

# Calculate the total number of schools using a merged dataframe
school_count= student_data_complete_df["school_name"].unique()

# Calculate the total budget for the entire dataset
total_budget = student_data_complete_df["budget"].sum()

# Calculate the overall average math score for all students across all schools
avg_math_score = student_data_complete_df["math_score"].mean()

# Calculate the overall average reading score for all students across all schools
avg_reading_score = student_data_complete_df["reading_score"].mean()

# Calculate the percentage of students passing math across all schools
percent_pass_math = passing_math_count / float(student_count) *100

# Calculate the percentage of students passing reading across all schools
percent_pass_reading = passing_reading_count / float(student_count) *100

# Calculate the total number of students passing math AND reading across all schools and then the overall passing percentage
overall_passing = passing_math_reading["student_name"].count()
overall_passing_percent = overall_passing / float(student_count)*100
```

In general, the impact was marginal.  The average reading scores for the district did not change significantly (slight change in the hundredths), and the average math scores only negative change by .1 between the original and updated data, respectively.  There was also a 1% point decrease across all three percentages (math, reading, and overall passing) for the district between the original and updated data.

>**Original District Summary**

![Original District Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/District%20Summary-Original.png)

>**Updated District Summary**

![Updated District Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/District_Summary-Updated.png)


**QUESTION: How is the school summary affected**

To calculate the performance of each school, we needed to create a new dataframe based off of metrics for each school.  We utilized the following code to accomplish this, paying particular attention to .count and .mean formulas.  

```
# Determine the school type, total budget per school, and budget per student for each school based off of the school dataframe
per_school_types = school_data_df.set_index(["school_name"])["type"]
per_school_budget = school_data_df.set_index(["school_name"])["budget"]
per_school_capita = per_school_budget / per_school_counts

# Calculate the total student count off of the combined dataframe
per_school_counts = student_data_complete_df["school_name"].value_counts()

# Calculate the average test scores off of the combined dataframe
per_school_math = student_data_complete_df.groupby(["school_name"]).mean()["math_score"]
per_school_reading = student_data_complete_df.groupby(["school_name"]).mean()["reading_score"]

# Calculate the percentage of passing math and reading scores per school off of the combined dataframe
per_school_passing_math = per_school_passing_math / per_school_counts * 100
per_school_passing_reading = per_school_passing_reading / per_school_counts * 100

# Calculate the students who passed both math and reading off of the combined dataframe and then count the number of those students
per_passing_math_reading = student_data_complete_df[(student_data_complete_df["math_score"] >= 70) & (student_data_complete_df["reading_score"] >= 70)]
per_passing_math_reading = per_passing_math_reading.groupby(["school_name"]).count()["student_name"]

# Calculate the overall passing percentage off of the combined dataframe
per_overall_passing_percentage = per_passing_math_reading / per_school_counts * 100
```

The overall scores for math did not have a significant change (decrease in the hundredth), but the reading scores increased slightly (83.8489 to 83.8960).  This increase is likely reflective of the overall scores in reading for the 9th grade at Thomas High School being lower than the other students, and thus bringing down the average as seen in the original metrics.

>**Original Thomas High School Summary**

![Original Thomas High School Summary*](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_Thomas-Original.png)

>**Updated Thomas High School Summary**

![Updated Thomas High School Summary*](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_Thomas-Updated.png)

**QUESTION: How does replacing the 9th graders’ math and reading scores affect Thomas High School’s performance relative to the other schools**

Once we calculated metrics for each school, we created data another dataframe and organized it based off of descending percentage of overall passing.  

```
# Combine metrics by school into single DataFrame.
per_school_summary_df = pd.DataFrame({
             "School Type": per_school_types,
             "Total Students": per_school_counts,
             "Total School Budget": per_school_budget,
             "Per Student Budget": per_school_capita,
             "Average Math Score": per_school_math,
           "Average Reading Score": per_school_reading,
           "% Passing Math": per_school_passing_math,
           "% Passing Reading": per_school_passing_reading,
           "% Overall Passing": per_overall_passing_percentage})

top_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=False)
```

Overall, there was a significant change in Thomas High School’s performance relative to other schools in the school district.  For example, with the original data, Thomas High School ranked second with percentage of students with overall passing.  With the new data, however, they dropped to 8th place.  Additionally, their percentage of students who passed math and percentage who passed reading also dropped significantly.  This is due to the fact that our calculations take into account the total number of students within the school, to include those who’s scores are identified as NaN.  

>**Original School Summary Ordered by Overall % Passed**

![Original School Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_%25Passing-Original.png)

>**Updated School Summary Ordered by Overall % Passed**

![Updated School Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_%25Passing-Updated.png)

**QUESTION: How does replacing the 9th graders’ scores affect the following:**

*A: Math and reading scores by grade*

As with the previous analysis based on schools, we used the following code to pull data from the merged dataframe to conduct analysis on the average score in math and then in reading for each grade within each school.  The code below is a sample of the code, and we replicated that for each additional grade.  We then created a new dataframe for average math scores and another new one for average reading scores.    

```
# Create a grade level DataFrames (repeat the below for each grade)
ninth_graders = student_data_complete_df[(student_data_complete_df["grade"] == "9th")]

# Group each school Series by the school name for the average math score (repeat the below for each grade)
ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]

# Group each school Series by the school name for the average reading score (repeat the below for each grade)
ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
```

Due to the fact that the errors and corrections were isolated to a specific grade, the impact for average math scores is only within the 9th grade scores.  Those scores turned from a numerical value to NaN.

>**Original Math Scores by Grade**

![Original Math Scores by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Original.png)

>**Updated Math Scores by Grade**

![Updated Math Scoresby Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Updated.png)


Like the math scores, due to the fact that the errors and corrections were isolated to a specific grade, the impact for average reading scores is only within the 9th grade scores.  Those scores turned from a numerical value to NaN.  Of note, you can see in the original data, that the 9th grade average reading scores were lower than the 10th and 12th grades, and only a tenth of a point higher than the 11th graders.  This confirms the assessment for the reason why the overall reading scores increase between the original and the updates within other summaries.

>**Original Reading Scores by Grade**

![Original Reading Scores by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Original.png)

>**Updated Reading Scores by Grade**

![Updated Reading Scores by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Updated.png)

*B: Scores by spending*

To understand the how the change in data impacts the outcome of per capita spending, we needed to first build out bins or ranges of spending.  Using the describe function, we were able to identify key attributes of school spending and create the following bins: 0-584, 585-629, 630-644, and 645-675.  We then cut the data based off of those bins and then grouped the results by them.  Cutting the data is a step not needed in the solutions above because the data above already had a field that we could group by.  Below is the final code to build out the calculations, from which we created another dataframe to display the data.

```
# Cut the per_school_capita into the spending ranges.
spending_bins = [0, 585, 630, 645, 675]
pd.cut(per_school_capita, spending_bins)

# Cut the per_school_capita into the spending ranges.
group_names = ["<$584", "$585-629", "$630-644", "$645-675"]

per_school_capita.groupby(pd.cut(per_school_capita, spending_bins)).count()

# Categorize spending based on the bins.
per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=group_names)

# Calculate averages for the desired columns.
spending_math_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]
spending_reading_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]
spending_passing_math = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]
spending_passing_reading = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
overall_passing_spending = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]
```

Thomas High School’s per capita spending is $638 per student, which places then into the middle bin, $630-644.  With the updated data, there is a negligible decrease in the average math scores (change in the thousandths) and a negligible increase in the average reading scores (change in the thousandths).  Of note, this positive change reinforces the assessment above that the 9th grade reading scores were slightly lower than the those of the other grades and their removal provided a small benefit.  The percentage changes, however, were noticeable with a decrease from 73% to 67% of students passing math, a decrease from 84% to 77% of students passing reading, and a decrease from 63% to 56% of students passing both parts of the exam.  An interesting data point here is the significant difference in overall passing percentages between the top two and bottom two spending bins.  There is a 25% difference between the $630-644 and the $585-629 bin, but only a 2% difference between the $630-644 and the $645-675 bin.  Additionally, the higher per capita schools have the lower overall percentage of students who pass.  

>**Original Scores & Passing by Spending**

![Original Scores & Passing by Spending](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Spending-Original.png)

>**Updated Scores & Passing by Spending**

![Updated Scores & Passing by Spending](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Spending-Updated.png)

*C: Scores by school size*

As with spending, in order to understand the impact the erroneous data had on the impact of school size on performance, we created bins for school size.  These bins were small (<1000 students), medium (1000-2000 students, and large (2000-5000 students).  We cut the merged dataframe based on school size into these bins and then calculated the appropriate performance metrics.  Below is the final code to build out the calculations, from which we created another dataframe to display the data.

```
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
group_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

# Categorize spending based on the bins.
per_school_summary_df["School Size"] = pd.cut(per_school_summary_df["Total Students"], size_bins, labels=group_names)

# Calculate averages for the desired columns.
size_math_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Math Score"]
size_reading_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Reading Score"]
size_passing_math = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Math"]
size_passing_reading = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Reading"]
size_overall_passing = per_school_summary_df.groupby(["School Size"]).mean()["% Overall Passing"]
```

The total student population at Thomas High School is 1635, which places then into the middle bin, 1000-2000 students.  With the updated data, there is a negligible decrease in the average math scores (change in the hundredths) and a slightly more noticeable increase in the average reading scores (change in the tens).  Of note, this positive change yet again reinforces the assessment above that the 9th grade reading scores were slightly lower than the those of the other grades and their removal provided a small benefit.  The percentage changes, however, were noticeable with a decrease from 94% to 88% of students passing math, a decrease from 97% to 91% of students passing reading, and a decrease from 91% to 85% of students passing both parts of the exam.

>**Original Scores & Passing by School Size**

![Original Scores & Passing by School Size](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Size-Original.png)

>**Updated Scores & Passing by School Size**

![Updated Scores & Passing by School Size](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Size-Updated.png)

*D: Score by school type*

Unlike the previous summaries, there is already a column and field attached to each record for the type of school within the merged dataset.  As such, we focused on calculating scores and percentage of students passing using the school type as a means to group our data.  Below is the code we used, and then took the outputs to create another dataframe to print.

```
# Calculate averages for the desired columns.
type_math_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Math Score"]
type_reading_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Reading Score"]
type_passing_math = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Math"]
type_passing_reading = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Reading"]
type_overall_passing = per_school_summary_df.groupby(["School Type"]).mean()["% Overall Passing"]
```

Thomas High School is a charter school.  With the updated data, there is a negligible decrease in the average math scores (change in the hundredths) and a slightly more noticeable increase in the average reading scores (change in the tens).  Of note, this positive change yet again reinforces the assessment above that the 9th grade reading scores were slightly lower than the those of the other grades and their removal provided a small benefit.  The percentage changes, however, were noticeable but less than the other summaries.  For example, the percentage of students passing math decreased from decrease from 94% to 90%, the percentage of students passing reading decrease from 97% to 94%, and the percentage of students passing both parts of the exam a decrease from 90% to 87%.  Regardless of the new data, charter schools did perform better than district schools as a whole.

>**Original Scores & Passing by School Type**

![Original Scores & Passing by School Type](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Type-Original.png)

>**Updated Scores & Passing by School Type**

![Updated Scores & Passing by School Type](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Type-Updated.png)

## Summary

*Change 1 -* 

*Change 2 -* 

*Change 3 -* 

*Change 4 -* 
