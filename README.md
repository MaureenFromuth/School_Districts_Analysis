# PyCity School Challenge

## Overview of Project

```
student_data_complete_df = pd.merge(student_data_df, school_data_df, on=["school_name"])
```


### Purpose  

## Results


Updated inputs for 9th Grade at Thomas High School
```
student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th") & (student_data_df["reading_score"] >0), "reading_score"] = np.nan

student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th") & (student_data_df["math_score"] >0), "math_score"] = np.nan
```


```
# Identify how many students are in the 9th grade at Thomas High School
student_data_df_remove = student_data_df[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th")]
len(student_data_df_remove)

# Check that the number of reading entries w/ NaN is equal to the number of entries in student_data_df_remove
student_data_df["reading_score"].isnull().sum()

# Check that the number of math entries w/ NaN is equal to the number of entries in student_data_df_remove
student_data_df["math_score"].isnull().sum()
```

**QUESTION: How is the district summary affected**


```
# Determine the number of total students 
student_count = student_data_complete_df["reading_score"].count()

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

>**Original District Summary**

![Original District Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/District%20Summary-Original.png)

>**Updated District Summary**

![Updated District Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/District_Summary-Updated.png)



**QUESTION: How is the school summary affected**


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

>**Original School Summary Ordered by Overall % Passed**

![Original School Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_%25Passing-Original.png)

>**Updated School Summary Ordered by Overall % Passed**

![Updated School Summary](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_%25Passing-Updated.png)



**QUESTION: How does replacing the 9th graders’ math and reading scores affect Thomas High School’s performance relative to the other schools**




**QUESTION: How does replacing the 9th graders’ scores affect the following:**

*A: Math and reading scores by grade*


```
# Create a grade level DataFrames (repeat the below for each grade)
ninth_graders = student_data_complete_df[(student_data_complete_df["grade"] == "9th")]

# Group each school Series by the school name for the average math score (repeat the below for each grade)
ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]

# Group each school Series by the school name for the average reading score (repeat the below for each grade)
ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
```

>**Original Math Scores by Grade**

![Original Math Scores by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Original.png)

>**Updated Math Scores by Grade**

![Updated Math Scoresby Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Updated.png)



>**Original Reading Scores by Grade**

![Original Reading Scores by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Original.png)

>**Updated Reading Scores by Grade**

![Updated Reading Scores by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Updated.png)

*B: Scores by spending*


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
>**Original Scores & Passing by Spending**

![Original Scores & Passing by Spending](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Spending-Original.png)

>**Updated Scores & Passing by Spending**

![Updated Scores & Passing by Spending](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Spending-Updated.png)

*C: Scores by school size*


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

>**Original Scores & Passing by School Size**

![Original Scores & Passing by School Size](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Size-Original.png)

>**Updated Scores & Passing by School Size**

![Updated Scores & Passing by School Size](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Size-Updated.png)

*D: Score by school type*


```
# Calculate averages for the desired columns.
type_math_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Math Score"]
type_reading_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Reading Score"]
type_passing_math = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Math"]
type_passing_reading = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Reading"]
type_overall_passing = per_school_summary_df.groupby(["School Type"]).mean()["% Overall Passing"]
```

>**Original Scores & Passing by School Type**

![Original Scores & Passing by School Type](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Type-Original.png)

>**Updated Scores & Passing by School Type**

![Updated Scores & Passing by School Type](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Type-Updated.png)

## Summary

*Change 1 -* 

*Change 2 -* 

*Change 3 -* 

*Change 4 -* 
