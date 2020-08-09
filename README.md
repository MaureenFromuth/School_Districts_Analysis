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

- **How is the district summary affected**


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



- **How is the school summary affected**


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

>**Original School Summary: Top Performers**

![Original School Summary: Top Performers](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/District%20Summary-Original.png)

>**Updated School Summary: Top Performers**

![Updated School Summary: Top Performers](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_Top-Updated.png)

>**Original School Summary: Bottom Performers**

![Original School Summary: Bottom Performers](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_Bottom-Original.png)

>**Updated School Summary: Bottom Performers**

![Updated School Summary: Bottom Performers](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/School_Summary_Bottom-Updated.png)


- **How does replacing the 9th graders’ math and reading scores affect Thomas High School’s performance relative to the other schools**


```
# Create a grade level DataFrames (repeat the below for each grade)
ninth_graders = student_data_complete_df[(student_data_complete_df["grade"] == "9th")]

# Group each school Series by the school name for the average math score (repeat the below for each grade)
ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]

# Group each school Series by the school name for the average reading score (repeat the below for each grade)
ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
```

>**Original Math Scores By Grade**

![Original Math Scores By Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Original.png)

>**Updated Math Scores By Grade**

![Updated Math Scores By Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Updated.png)


>**Original Math Scores By Grade**

![Original Reading Scores By Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Original.png)

>**Updated Reading Scores By Grade**

![Updated Reading Scores By Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Updated.png)



- **How does replacing the 9th graders’ scores affect the following:**

*Math and reading scores by grade*


```
```

>**Original Math Scores by Grade**

![Original Math Scores & Passing by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Original.png)

>**Updated Math Scores by Grade**

![Updated Math Scores & Passing by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Math_Score_by_Grade-Updated.png)



>**Original Reading Scores by Grade**

![Original Reading Scores & Passing by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Original.png)

>**Updated Reading Scores by Grade**

![Updated Reading Scores & Passing by Grade](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Reading_Score_by_Grade-Updated.png)

*Scores by spending*


```
```
>**Original Scores & Passing by Spending**

![Original Scores & Passing by Spending](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Spending-Original.png)

>**Updated Scores & Passing by Spending**

![Updated Scores & Passing by Spending](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Spending-Updated.png)

*Scores by school size*


```
```

>**Original Scores & Passing by School Size**

![Original Scores & Passing by School Size](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Size-Original.png)

>**Updated Scores & Passing by School Size**

![Updated Scores & Passing by School Size](https://github.com/MaureenFromuth/School_Districts_Analysis/blob/master/Scores_Passing_by_Size-Updated.png)

*Score by school type*


```
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
