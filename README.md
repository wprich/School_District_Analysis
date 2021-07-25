# School_District_Analysis

## Overview

   We were given some data on various schools within a school district from a client.  The client wanted a summary of test scores and other miscellaneous items in a report to present to the school board.  While formatting the data and setting it up it was brought to our attention that there was suspicion of cheating among students at one school.  In particular, the 9th graders at Thomas High School.  We were then asked to change the values for this grade level across that schools test scores to a null value and run the analysis again to see what the differences were and how noticable they are.  This comparative analysis was done since most of the work was already done for the previous request of the client so only a few extra steps were needed to adjust the data accordingly.  
  
## Results
https://nbviewer.jupyter.org/github/wprich/School_District_Analysis/blob/main/PyCitySchools_Challenge.ipynb
https://github.com/wprich/School_District_Analysis/blob/main/PyCitySchools_Challenge.ipynb

  A nbviewer link has been provided since some of the data frames will not present correctly on the github layout, the inputs are still the same.  Snippets of code will be provided and looked at more closely to give insight to conclusions.
  
  * The district summary was affected by the scores for Thomas High School altering since the 9th graders test scores were no longer applicable to their percentages.  
     Pre-changes
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_Edit_Disctrict_Summary.png)
     Post-changes
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_Edit_District_Summary.png)
    As you can see, not much was changed at all by altering the 9th graders grades from Thomas High School.  Only minimal differences can be observed.
  * The school summary on the other hand shows a significant bump in percentages passed on both math and reading categories for Thomas High School.  It also shows a bump in the over all passing percentage for that school as well.
      This required getting a new student count and then calculation new passing percentages.  The new values would then be replacing the old values that were in the current           school summary.
    ~~~
    new_thomas_students_count = len(school_data_complete_df.loc[(school_data_complete_df["school_name"] == "Thomas High School") & (school_data_complete_df["grade"] != "9th")])
    new_thomas_students_count
    1174
    thomas_passing_reading_and_math_percentage = thomas_passing_reading_and_math_count / float(new_thomas_students_count) * 100
    print(thomas_passing_reading_and_math_percentage)
    90.63032367972743
    per_school_summary_df.loc[['Thomas High School'], ['% Overall Passing']]= thomas_passing_reading_and_math_percentage
    ~~~
     Pre-changes
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_Edit_School_Summary.png)
     Post-changes
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_Edit_School_Summary.png)

  * Another comparison tool implemented was the comparison of top and bottom performing schools.  In both edits, the pre changes and post changes, Thomas High School was in 2nd place compared to the other schools.  The grading criteria was overall passing percentage.  Again, the changes in the actual percentages for Thomas High School itself were negligable.  The code used for this was a sort function of pandas and choosing ascending or descending order.
       ~~~
        # Sort and show top five schools.
        top_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=False)
      ~~~
    Pre-changes
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_9th_Grade_Edits.png)
    Post-changes
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_9th_Grade_Edits.png)
        
   * There were four other criteria for comparisons as well: scores by grade levels, scores by school spending, scores by school size, and scores by school type.  The most glaring difference was the scores by grade as the scores for the 9th graders in Thomas High School were not accounted for and dropped due to the suspected cheating.  The code for these sections were somewhat tricky as we had to use a considerable number of conditionals to compile the grades and scores into a single data frame.
   ~~~
   # Create a Series of scores by grade levels using conditionals.
ninth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "9th")]

tenth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "10th")]

eleventh_graders = school_data_complete_df[(school_data_complete_df["grade"] == "11th")]

twelfth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "12th")]

# Group each school Series by the school name for the average math score.
ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]

tenth_grade_math_scores = tenth_graders.groupby(["school_name"]).mean()["math_score"]

eleventh_grade_math_scores = eleventh_graders.groupby(["school_name"]).mean()["math_score"]

twelfth_grade_math_scores = twelfth_graders.groupby(["school_name"]).mean()["math_score"]

# Group each school Series by the school name for the average reading score.
ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]

tenth_grade_reading_scores = tenth_graders.groupby(["school_name"]).mean()["reading_score"]

eleventh_grade_reading_scores = eleventh_graders.groupby(["school_name"]).mean()["reading_score"]

twelfth_grade_reading_scores = twelfth_graders.groupby(["school_name"]).mean()["reading_score"]
# Combine each Series for average math scores by school into single data frame.
math_scores_by_grade = pd.DataFrame({
               "9th": ninth_grade_math_scores,
               "10th": tenth_grade_math_scores,
               "11th": eleventh_grade_math_scores,
               "12th": twelfth_grade_math_scores})

# Display DataFrame
math_scores_by_grade
   ~~~
   
   Pre-changes on scores by grade levels in math
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_Math_Scores.png)
   Post-changes on scores by grade levels in math
        ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_Math_Scores.png)
     The reading charts have a similar outcome.  Links will be provided for any interested in seeing them as well.
          https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_Reading_Scores.png
          https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_Reading_Scores.png
    
   Next is the spending summary differences, of which there aren't any.  The school spent the same amount on each student regardless of scores.  The code for these follows a similar pattern as the previous data frames.  The main difference being that "bins" had to be created to hold different values for criteria that the data was being sorted by.  
   ~~~
# Establish the spending bins and group names.
per_school_capita.describe()
spending_bins = [0, 585, 630, 645, 675]
group_names = ["<$584", "$585-629", "$630-644", "$645-675"]

# Categorize spending based on the bins.
per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=group_names)
# Calculate averages for the desired columns. 
spending_math_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]

spending_reading_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]

spending_passing_math = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]

spending_passing_reading = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]

overall_passing_spending = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]
# Create the DataFrame
spending_summary_df = pd.DataFrame({
          "Average Math Score" : spending_math_scores,
          "Average Reading Score": spending_reading_scores,
          "% Passing Math": spending_passing_math,
          "% Passing Reading": spending_passing_reading,
          "% Overall Passing": overall_passing_spending})
# Format the DataFrame 
spending_summary_df["Average Math Score"] = spending_summary_df["Average Math Score"].map("{:.1f}".format)

spending_summary_df["Average Reading Score"] = spending_summary_df["Average Reading Score"].map("{:.1f}".format)

spending_summary_df["% Passing Math"] = spending_summary_df["% Passing Math"].map("{:.0f}".format)

spending_summary_df["% Passing Reading"] = spending_summary_df["% Passing Reading"].map("{:.0f}".format)

spending_summary_df["% Overall Passing"] = spending_summary_df["% Overall Passing"].map("{:.0f}".format)

# Display DataFrame
spending_summary_df
   ~~~
      
   Pre-changes to spending summary   
   ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_Spending_Summary.png)
   Post-changes to spending summary
   ![alt text](https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_Spending_Summary.png)

   Lastly, both school size and school type were not affected at all by the changes.  Links will be provided for any interested in seeing their results as well.  The code for both of these follows a very similar pattern to the spending summary.
   
   School Size
    https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_School_Size.png
    https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_School_Size.png
    
   School Type
    https://github.com/wprich/School_District_Analysis/blob/main/Resources/Pre_School_Type.png
    https://github.com/wprich/School_District_Analysis/blob/main/Resources/Post_School_Type.png
    
  
## Summary
  There are numerous differences to the school analysis after replacing the grades of the 9th graders at Thomas High School.  Most notably being that the school summary has drastically shown a brighter light on Thomas High School.  The schools overall percentage passing jumped 25%, going from 65% to 90%.  This tells us that the scores of the 9th graders were severally hindering the school.  Also a change this drastic could be used as evidence of grade tampering.  More investigative analysis would have to be done in order to come to a solid conclusion.  Another change was the grade scores.  Its hard to miss having all the cells filled to seeing a NaN in place of the 9th graders grades for Thomas High School.  At first glance this could seem like a mistake but then we would have to tell the client that the changes were indeed asked for.  Another change was the top 5 school summary.  Thomas High School is on both lists but the numbers on both data frames are very similar but still different.  This shows us that Thomas High Schools placement among the top schools was not affected by the changes.  Lastly, the last change that is noticable is the distrtict summary.  However, the changes are so minute that they barely warrant being mentioned.  The numbers are different by only a few decimal places in the thenths place.  
    
