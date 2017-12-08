
# Py City Schools

### Observation Trends
#### Observation 1: Overall Reading percentages are greater compared to Overall Math percentages. 
#### Observation 2: Charter-type Schools have better performance compared to District-type Schools. 
#### Observation 3: The School Spending budget on each student is inversely proportional to the overall pass percentages of the school.
#### Observation 4: Large sized Schools has lower overall pass percentage compared to Medium and Small sized Schools.


```python
#Import Dependencies
import pandas as pd
import os
import numpy as np
```

### Reading both CSV files(Students and Schools)


```python
#File Path for students
StudentsFilePath=os.path.join("raw_data","students_complete.csv")
#Open and read the file
Student_DF=pd.read_csv(StudentsFilePath)
```


```python
#File Path for School
SchoolFilePath=os.path.join("raw_data","schools_complete.csv")
#Open and read the file
Schools_DF=pd.read_csv(SchoolFilePath)
```


```python
#Renaming both dataframe school name column to the same name
Student_DF=Student_DF.rename(columns={"school":"School Name"})
Schools_DF=Schools_DF.rename(columns={"name":"School Name"})
```

## District Summary


```python
#District Summary
Total_Students=len(Student_DF["Student ID"].unique())
District_Summary=pd.DataFrame({"Total Schools":[len(Schools_DF["School ID"].unique())],
                               "Total Students":[Total_Students],
                               "Total Budget":"$"+str(Schools_DF["budget"].sum()),
                               "Average Math Score":[round(Student_DF["math_score"].mean(),2)],
                               "Average Reading Score":[round(Student_DF["reading_score"].mean(),2)],
                               "% Passing Math":[(len(Student_DF[Student_DF["math_score"]>=70])/Total_Students)*100],
                               "% Passing Reading":[(len(Student_DF[Student_DF["reading_score"]>=70])/Total_Students)*100]
                              })
District_Summary["% Overall Passing Rate"]=(District_Summary["% Passing Math"]+District_Summary["% Passing Reading"])/2

#Rearranging the order of columns
District_Summary=District_Summary[["Total Schools","Total Students","Total Budget",
                                   "Average Math Score","Average Reading Score",
                                   "% Passing Math","% Passing Reading","% Overall Passing Rate"]]

#Formatting the columns to the desire format
District_Summary["% Passing Math"]=District_Summary["% Passing Math"].map("{:.2f}%".format)
District_Summary["% Passing Reading"]=District_Summary["% Passing Reading"].map("{:.2f}%".format)
District_Summary["% Overall Passing Rate"]=District_Summary["% Overall Passing Rate"].map("{:.2f}%".format)

District_Summary
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>$24649428</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>74.98%</td>
      <td>85.81%</td>
      <td>80.39%</td>
    </tr>
  </tbody>
</table>
</div>



## School Summary


```python
#Combined school and student data
School_data_complete=pd.merge(Schools_DF,Student_DF,on="School Name",how="outer").groupby(["School Name"],as_index=True)

#Calculating and storing all value of each school into lists
School_Type=School_data_complete["type"].unique().str.get(0)
Total_Budget=School_data_complete["budget"].unique().str.get(0)

Total_Students=School_data_complete.count()["Student ID"]
per_Student_Budget=Total_Budget/Total_Students
avg_math_score=School_data_complete.mean()["math_score"]
avg_reading_score=School_data_complete.mean()["reading_score"]

Count_Percent_Passing_Math=Student_DF.loc[Student_DF["math_score"]>=70,:].groupby([Student_DF["School Name"]],as_index=True).count()["math_score"]
Percent_Passing_Math=round(Count_Percent_Passing_Math*100/Total_Students,2)

Count_Percent_Passing_Reading=Student_DF.loc[Student_DF["reading_score"]>=70,:].groupby([Student_DF["School Name"]],as_index=True).count()["reading_score"]
Percent_Passing_Reading=round(Count_Percent_Passing_Reading*100/Total_Students,2)

Percent_Overall_Passing=round((Percent_Passing_Math+Percent_Passing_Reading)/2,2)

#School Summary DataFrame
School_Summary=pd.DataFrame({
                             "School Type":School_Type,
                             "Total Students":Total_Students,
                             "Total School Budget":Total_Budget,
                             "Per Student Budget":per_Student_Budget,
                             "Average Math Score":round(avg_math_score,2),
                             "Average Reading Score":round(avg_reading_score,2),
                             "% Passing Maths":Percent_Passing_Math,
                             "% Passing Reading":Percent_Passing_Reading,
                             "% Overall Passing Rate":Percent_Overall_Passing
                            })

#Formatting the columns to the desire format
School_Summary["Total School Budget"]=School_Summary["Total School Budget"].map("${:,}".format)
School_Summary["Per Student Budget"]=School_Summary["Per Student Budget"].astype(int).map("${}".format)
School_Summary["% Passing Maths"]=School_Summary["% Passing Maths"].map("{:.2f}%".format)
School_Summary["% Passing Reading"]=School_Summary["% Passing Reading"].map("{:.2f}%".format)
School_Summary["% Overall Passing Rate"]=School_Summary["% Overall Passing Rate"].map("{:.2f}%".format)

#Rearranging the order of columns
School_Summary=School_Summary[["School Type","Total Students","Total School Budget",
                               "Per Student Budget","Average Math Score","Average Reading Score",
                               "% Passing Maths","% Passing Reading","% Overall Passing Rate"]]

School_Summary
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Maths</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4976</td>
      <td>$3,124,928</td>
      <td>$628</td>
      <td>77.05</td>
      <td>81.03</td>
      <td>66.68%</td>
      <td>81.93%</td>
      <td>74.31%</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>$1,081,356</td>
      <td>$582</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>94.13%</td>
      <td>97.04%</td>
      <td>95.58%</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411</td>
      <td>$639</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>65.99%</td>
      <td>80.74%</td>
      <td>73.36%</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>$1,763,916</td>
      <td>$644</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>68.31%</td>
      <td>79.30%</td>
      <td>73.81%</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>$917,500</td>
      <td>$625</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>93.39%</td>
      <td>97.14%</td>
      <td>95.26%</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>$3,022,020</td>
      <td>$652</td>
      <td>77.29</td>
      <td>80.93</td>
      <td>66.75%</td>
      <td>80.86%</td>
      <td>73.81%</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087</td>
      <td>$581</td>
      <td>83.80</td>
      <td>83.81</td>
      <td>92.51%</td>
      <td>96.25%</td>
      <td>94.38%</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>$1,910,635</td>
      <td>$655</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>65.68%</td>
      <td>81.32%</td>
      <td>73.50%</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>$3,094,650</td>
      <td>$650</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>66.06%</td>
      <td>81.22%</td>
      <td>73.64%</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858</td>
      <td>$609</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>94.59%</td>
      <td>95.95%</td>
      <td>95.27%</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>$2,547,363</td>
      <td>$637</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>66.37%</td>
      <td>80.22%</td>
      <td>73.30%</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1761</td>
      <td>$1,056,600</td>
      <td>$600</td>
      <td>83.36</td>
      <td>83.73</td>
      <td>93.87%</td>
      <td>95.85%</td>
      <td>94.86%</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>$1,043,130</td>
      <td>$638</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>93.27%</td>
      <td>97.31%</td>
      <td>95.29%</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>$1,319,574</td>
      <td>$578</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>93.87%</td>
      <td>96.54%</td>
      <td>95.21%</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1800</td>
      <td>$1,049,400</td>
      <td>$583</td>
      <td>83.68</td>
      <td>83.96</td>
      <td>93.33%</td>
      <td>96.61%</td>
      <td>94.97%</td>
    </tr>
  </tbody>
</table>
</div>



## Top Performing School


```python
#Top Performing Schools based on Overall Passing Rate
Top_Performing_Schools= School_Summary.sort_values("% Overall Passing Rate",ascending=False).head()

Top_Performing_Schools
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Maths</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>$1,081,356</td>
      <td>$582</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>94.13%</td>
      <td>97.04%</td>
      <td>95.58%</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>$1,043,130</td>
      <td>$638</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>93.27%</td>
      <td>97.31%</td>
      <td>95.29%</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858</td>
      <td>$609</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>94.59%</td>
      <td>95.95%</td>
      <td>95.27%</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>$917,500</td>
      <td>$625</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>93.39%</td>
      <td>97.14%</td>
      <td>95.26%</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>$1,319,574</td>
      <td>$578</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>93.87%</td>
      <td>96.54%</td>
      <td>95.21%</td>
    </tr>
  </tbody>
</table>
</div>



## Bottom Performing School


```python
#Bottom Performing Schools based on Overall Passing Rate
Bottom_Performing_Schools= School_Summary.sort_values("% Overall Passing Rate",ascending=True).head()

Bottom_Performing_Schools
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Maths</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>$2,547,363</td>
      <td>$637</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>66.37%</td>
      <td>80.22%</td>
      <td>73.30%</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411</td>
      <td>$639</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>65.99%</td>
      <td>80.74%</td>
      <td>73.36%</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>$1,910,635</td>
      <td>$655</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>65.68%</td>
      <td>81.32%</td>
      <td>73.50%</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>$3,094,650</td>
      <td>$650</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>66.06%</td>
      <td>81.22%</td>
      <td>73.64%</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>$1,763,916</td>
      <td>$644</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>68.31%</td>
      <td>79.30%</td>
      <td>73.81%</td>
    </tr>
  </tbody>
</table>
</div>



## Math Scores based on Grades for each School


```python
#Math Scores by grade
Grouped_SchoolName_Grade=Student_DF.groupby([Student_DF["School Name"],Student_DF["grade"]],as_index=False)
Math_Score_Grade_DF=Grouped_SchoolName_Grade["math_score"].mean()

#Creating lists
math_SchoolNames=[]
math_mean_grade_9th=[]
math_mean_grade_10th=[]
math_mean_grade_11th=[]
math_mean_grade_12th=[]


for school in Math_Score_Grade_DF["School Name"].unique():
    School_Name=school
    math_SchoolNames.append(School_Name)
    index=math_SchoolNames.index(school)
    math_mean_grade_9th.insert(index,round(Math_Score_Grade_DF.loc[(Math_Score_Grade_DF["School Name"]==school) &  (Math_Score_Grade_DF["grade"]=="9th"),"math_score"].values[0],2))   
    math_mean_grade_10th.insert(index,round(Math_Score_Grade_DF.loc[(Math_Score_Grade_DF["School Name"]==school) &  (Math_Score_Grade_DF["grade"]=="10th"),"math_score"].values[0],2))   
    math_mean_grade_11th.insert(index,round(Math_Score_Grade_DF.loc[(Math_Score_Grade_DF["School Name"]==school) &  (Math_Score_Grade_DF["grade"]=="11th"),"math_score"].values[0],2))   
    math_mean_grade_12th.insert(index,round(Math_Score_Grade_DF.loc[(Math_Score_Grade_DF["School Name"]==school) &  (Math_Score_Grade_DF["grade"]=="12th"),"math_score"].values[0],2)) 

#Creating the dataframe of Math Scores based on the Grade
New_Math_DF=pd.DataFrame({"School Name":math_SchoolNames,
                          "9th":math_mean_grade_9th,
                          "10th":math_mean_grade_10th,
                          "11th":math_mean_grade_11th,
                          "12th":math_mean_grade_12th})

#Rearranging the columns
New_Math_DF=New_Math_DF[["School Name","9th","10th","11th","12th"]]

#Setting the index
New_Math_DF.set_index("School Name",inplace=True)

New_Math_DF
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.08</td>
      <td>77.00</td>
      <td>77.52</td>
      <td>76.49</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.09</td>
      <td>83.15</td>
      <td>82.77</td>
      <td>83.28</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.40</td>
      <td>76.54</td>
      <td>76.88</td>
      <td>77.15</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.36</td>
      <td>77.67</td>
      <td>76.92</td>
      <td>76.18</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.04</td>
      <td>84.23</td>
      <td>83.84</td>
      <td>83.36</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.44</td>
      <td>77.34</td>
      <td>77.14</td>
      <td>77.19</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.79</td>
      <td>83.43</td>
      <td>85.00</td>
      <td>82.86</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.03</td>
      <td>75.91</td>
      <td>76.45</td>
      <td>77.23</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.19</td>
      <td>76.69</td>
      <td>77.49</td>
      <td>76.86</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.63</td>
      <td>83.37</td>
      <td>84.33</td>
      <td>84.12</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.86</td>
      <td>76.61</td>
      <td>76.40</td>
      <td>77.69</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.42</td>
      <td>82.92</td>
      <td>83.38</td>
      <td>83.78</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.59</td>
      <td>83.09</td>
      <td>83.50</td>
      <td>83.50</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.09</td>
      <td>83.72</td>
      <td>83.20</td>
      <td>83.04</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.26</td>
      <td>84.01</td>
      <td>83.84</td>
      <td>83.64</td>
    </tr>
  </tbody>
</table>
</div>



## Reading Scores based on Grades for each School


```python
#Reading Scores by grade
Grouped_SchoolName_Grade=Student_DF.groupby([Student_DF["School Name"],Student_DF["grade"]],as_index=False)
Reading_Score_Grade_DF=Grouped_SchoolName_Grade["reading_score"].mean()

#Creating lists
reading_SchoolNames=[]
reading_mean_grade_9th=[]
reading_mean_grade_10th=[]
reading_mean_grade_11th=[]
reading_mean_grade_12th=[]


for school in Reading_Score_Grade_DF["School Name"].unique():
    School_Name=school
    reading_SchoolNames.append(School_Name)
    index=reading_SchoolNames.index(school)
    reading_mean_grade_9th.insert(index,round(Reading_Score_Grade_DF.loc[(Reading_Score_Grade_DF["School Name"]==school) &  (Reading_Score_Grade_DF["grade"]=="9th"),"reading_score"].values[0],2))   
    reading_mean_grade_10th.insert(index,round(Reading_Score_Grade_DF.loc[(Reading_Score_Grade_DF["School Name"]==school) &  (Reading_Score_Grade_DF["grade"]=="10th"),"reading_score"].values[0],2))   
    reading_mean_grade_11th.insert(index,round(Reading_Score_Grade_DF.loc[(Reading_Score_Grade_DF["School Name"]==school) &  (Reading_Score_Grade_DF["grade"]=="11th"),"reading_score"].values[0],2))   
    reading_mean_grade_12th.insert(index,round(Reading_Score_Grade_DF.loc[(Reading_Score_Grade_DF["School Name"]==school) &  (Reading_Score_Grade_DF["grade"]=="12th"),"reading_score"].values[0],2)) 

#Creating the dataframe of Math Scores based on the Grade
New_Reading_DF=pd.DataFrame({"School Name":reading_SchoolNames,
                             "9th":reading_mean_grade_9th,
                             "10th":reading_mean_grade_10th,
                             "11th":reading_mean_grade_11th,
                             "12th":reading_mean_grade_12th})

#Rearranging the columns
New_Reading_DF=New_Reading_DF[["School Name","9th","10th","11th","12th"]]

#Setting the index
New_Reading_DF.set_index("School Name",inplace=True)

New_Reading_DF
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.30</td>
      <td>80.91</td>
      <td>80.95</td>
      <td>80.91</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.68</td>
      <td>84.25</td>
      <td>83.79</td>
      <td>84.29</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.20</td>
      <td>81.41</td>
      <td>80.64</td>
      <td>81.38</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.63</td>
      <td>81.26</td>
      <td>80.40</td>
      <td>80.66</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.37</td>
      <td>83.71</td>
      <td>84.29</td>
      <td>84.01</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.87</td>
      <td>80.66</td>
      <td>81.40</td>
      <td>80.86</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.68</td>
      <td>83.32</td>
      <td>83.82</td>
      <td>84.70</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.29</td>
      <td>81.51</td>
      <td>81.42</td>
      <td>80.31</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.26</td>
      <td>80.77</td>
      <td>80.62</td>
      <td>81.23</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.81</td>
      <td>83.61</td>
      <td>84.34</td>
      <td>84.59</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.99</td>
      <td>80.63</td>
      <td>80.86</td>
      <td>80.38</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.12</td>
      <td>83.44</td>
      <td>84.37</td>
      <td>82.78</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.73</td>
      <td>84.25</td>
      <td>83.59</td>
      <td>83.83</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.94</td>
      <td>84.02</td>
      <td>83.76</td>
      <td>84.32</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.83</td>
      <td>83.81</td>
      <td>84.16</td>
      <td>84.07</td>
    </tr>
  </tbody>
</table>
</div>



## Scores based on the School Spending for each student


```python
#Scores by School Spending
bins=[0,584,614,644,675]
label_value=["<$585","$585-615","$615-645","$645-675"]

New_School_Summary=School_Summary
New_School_Summary["% Passing Maths"]=round(New_School_Summary["% Passing Maths"].str.replace("%", '').astype(float),2)
New_School_Summary["% Passing Reading"]=round(New_School_Summary["% Passing Reading"].str.replace("%", '').astype(float),2)
New_School_Summary["% Overall Passing Rate"]=round(New_School_Summary["% Overall Passing Rate"].str.replace("%", '').astype(float),2)

#Cutting based on the bins
New_School_Summary["Spending Ranges (Per Student)"]=pd.cut(New_School_Summary["Per Student Budget"].str.replace("$", '').astype(float),bins,labels=label_value)

#Grouping based on Spending Ranges (Per Student)
Grouped_School_Summary_Spending=New_School_Summary.groupby("Spending Ranges (Per Student)",as_index=False).mean()

#Extracting the required columns from the dataframe
Grouped_School_Summary_Spending=Grouped_School_Summary_Spending[["Spending Ranges (Per Student)","Average Math Score",
                                                                 "Average Reading Score","% Passing Maths",
                                                                 "% Passing Reading","% Overall Passing Rate"]]

#Setting the index
Grouped_School_Summary_Spending.set_index("Spending Ranges (Per Student)",inplace=True)

Grouped_School_Summary_Spending
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Maths</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges (Per Student)</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;$585</th>
      <td>83.452500</td>
      <td>83.935000</td>
      <td>93.460000</td>
      <td>96.610000</td>
      <td>95.035000</td>
    </tr>
    <tr>
      <th>$585-615</th>
      <td>83.600000</td>
      <td>83.885000</td>
      <td>94.230000</td>
      <td>95.900000</td>
      <td>95.065000</td>
    </tr>
    <tr>
      <th>$615-645</th>
      <td>79.078333</td>
      <td>81.891667</td>
      <td>75.668333</td>
      <td>86.106667</td>
      <td>80.888333</td>
    </tr>
    <tr>
      <th>$645-675</th>
      <td>76.996667</td>
      <td>81.026667</td>
      <td>66.163333</td>
      <td>81.133333</td>
      <td>73.650000</td>
    </tr>
  </tbody>
</table>
</div>



## Scores based on the School Size (i.e., Total Students in each school)


```python
#Scores by School Size
bins=[0,999,1999,5000]
label_value=["Small (<1000)","Medium (1000-2000)","Large (2000-5000)"]

#Cutting based on the bins
New_School_Summary["School Size"]=pd.cut(New_School_Summary["Total Students"],bins,labels=label_value)

#Grouping based on School Size
Grouped_School_Summary_Size=New_School_Summary.groupby("School Size",as_index=False).mean()

#Setting the index
Grouped_School_Summary_Size.set_index("School Size",inplace=True)

#Extracting the required columns from the dataframe
Grouped_School_Summary_Size=Grouped_School_Summary_Size[["Average Math Score","Average Reading Score",
                                                         "% Passing Maths","% Passing Reading","% Overall Passing Rate"]]

Grouped_School_Summary_Size

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Maths</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small (&lt;1000)</th>
      <td>83.820</td>
      <td>83.92500</td>
      <td>93.55000</td>
      <td>96.10000</td>
      <td>94.8250</td>
    </tr>
    <tr>
      <th>Medium (1000-2000)</th>
      <td>83.374</td>
      <td>83.86800</td>
      <td>93.59800</td>
      <td>96.79000</td>
      <td>95.1920</td>
    </tr>
    <tr>
      <th>Large (2000-5000)</th>
      <td>77.745</td>
      <td>81.34375</td>
      <td>69.96375</td>
      <td>82.76625</td>
      <td>76.3675</td>
    </tr>
  </tbody>
</table>
</div>



## Scores based on the School Type


```python
#Scores by School Type

#Grouping based on School Type
Grouped_School_Summary_Type=New_School_Summary.groupby("School Type",as_index=False).mean()

#Setting the index
Grouped_School_Summary_Type.set_index("School Type",inplace=True)

#Extracting the required columns from the dataframe
Grouped_School_Summary_Type=Grouped_School_Summary_Type[["Average Math Score","Average Reading Score",
                                                         "% Passing Maths","% Passing Reading","% Overall Passing Rate"]]

Grouped_School_Summary_Type
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Maths</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.472500</td>
      <td>83.897500</td>
      <td>93.620000</td>
      <td>96.586250</td>
      <td>95.102500</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.955714</td>
      <td>80.965714</td>
      <td>66.548571</td>
      <td>80.798571</td>
      <td>73.675714</td>
    </tr>
  </tbody>
</table>
</div>


