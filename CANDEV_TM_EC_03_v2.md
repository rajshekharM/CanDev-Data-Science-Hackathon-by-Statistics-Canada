

```python
#CANDEV Data Chellenge
#20th to 21st Oct, 2018
#uOttawa, Canada

#Team ID: EC_03
#Rajshekar Mukherjee (PRT 269)
#Arslan Ahmed (PRT 267)
#Yanpeng Gao (PRT 112)
#Japhet Bukongo (PRT 163)
```


```python
#source of instructions: https://github.com/claireaustin/Hackathon_CanDev2018-10-20
```


```python
import tensorflow as tf
device_name = tf.test.gpu_device_name()
if device_name != '/device:GPU:0':
  raise SystemError('GPU device not found')
print('Found GPU at: {}'.format(device_name))
```

    Found GPU at: /device:GPU:0
    


```python
import pandas as pd
import numpy as np
from xml.etree import ElementTree as ET
# from path import path
```


```python
from google.colab import files
uploaded = files.upload()
```



     <input type="file" id="files-0cf89739-598c-418c-a7b8-b7372a9510a5" name="files[]" multiple disabled />
     <output id="result-0cf89739-598c-418c-a7b8-b7372a9510a5">
      Upload widget is only available when the cell has been executed in the
      current browser session. Please rerun this cell to enable.
      </output>
      <script src="/nbextensions/google.colab/files.js"></script> 


    Saving OHIDataSet.csv to OHIDataSet (1).csv
    


```python
df_original = pd.read_csv("OHIDataSet.csv")
```


```python
df=df_original.copy()
df.head()
```


```python
string_file = uploaded.bytes()
```


```python
# 2b-1


def b1_check_json_xml(s):
    
    result= True
    try:
      x = ET.fromstring(s)
    except  e:
      xml=False
    try:
      json_object = json.loads(s)
    except e:
      json=False
      
    message="xml is " xml
    message+="json is " json
    
    if xml==False and json==False:
      result=False

  return result, message
    
result, message = b1_check_json_xml(string_file)
print (result, message)   
```


```python
# 2b-2


def b2_check_3nf(s):
    
    result= True
      
    message="Steps to find the highest normal form of a relation:\nFind all possible candidate keys of the relation.\nDivide all attributes into two categories: prime attributes and non-prime attributes.\nCheck for 1st normal form then 2nd and so on. If it fails to satisfy nth normal form condition, highest normal form will be n-1."
    message+="\nif n-1 is 3, then its 3rd normal form"
    return result, message
    
result, message = b2_check_3nf(df)
print (result, message)   
```


```python
# 2b-3


def b3_check_not_relational(s):
    result10, message = b10_check_column_names(df)
    result6 = b6_check_columnNames_unique(df)
    message=""
    result=True
    if (result10 and result6):
      for k in range(0,len(df.columns)):
        alpha=type(df.iloc[0,k])
        if not all(isinstance(df.iloc[1:,k], alpha)):
            result = False
            message+="\ncolumn"+k+"has not every row of same type",
    else:
      result=False
    
result = b3_check_not_relational(df)
print (result,message)   
```


```python
# 2b-6


def b6_check_columnNames_unique(df):
    result = False
    message=""
    for k in range(0,len(df.columns)):
      if isinstance(df.iloc[0,k], str):
        message="all columns are string"
        if not any(df.iloc[:,k].duplicated()):
          message+="\ncolumn name is not repeated in same row"
          result = True
      return result,message
    
result,message = b6_check_columnNames_unique(df)
print (result)
print (message)
```


```python
# 2b-10-Do all the columns have a column name? (i.e. variable name)
def b10_check_column_names(dataframe):
    column_names = df.columns.values.tolist()
    counter = 1
    error_message = ""
    result = True
    for header in column_names:
        if header.startswith("Unnamed: "):
            error_message += (" Unnamed column in Column " + str(counter)+",")
            result = False
        counter+=1
    return result, error_message
    
result, message = b10_check_column_names(df)
print (result, message)   
```


```python
# 2b-11-Are the column names consistent with the documentation?
#Compares the columns in the dataframe with a list the user provides  
def b11_column_names_documentation(dataframe, documented_columns):
    column_names = df.columns.values.tolist()
    column_names = [name.lower() for name in column_names]
    documented_columns = [name.lower() for name in documented_columns]
    
    error_message = ""
    result = True
    
    for column in documented_columns:
        if column not in column_names:
            result = False
            error_message += (column + " should be a column in our dataframe, ")
    return result, error_message,
    
result, message = b11_column_names_documentation(df, ["scenario","goals","long_goal",
                                             "dimension", "region_id", "region_name",
                                             "value"])
print (result, message)  
```


```python
# 2b-12
#Where possible, is human understable information preferred over coded information 
#(e.g., "cat", "dog" instead of "1", "2" to represent cat and dog, respectively).

def b12_categorical_data(df):
    length = df.shape[0]
    column_names = df.columns.values.tolist()
    for column in column_names:
        if (df[column].dtype == "object"):
            for i in df[column]:
                if i.isdigit():
                    return false, (column + " column should have representative data")
    return True, ""

b12_categorical_data(df)
```


```python
#2b-13
#Does each record (row) have a unique identifier?

def b13_unique_id(df):
    for column_name in df:
        column = df[column_name]
        if (column.dtype == "int64" and np.array_equal(column, column.astype(int))):
            if (column.value_counts().tolist()[0]) == 1:
                return True, ""
            
    return False, "There was no unique identifier"
        
b13_unique_id(df)
```


```python
#2b-14
#Can the tables in a data collection be linked via common fields (columns)?
def b14_linked_columns(df):
    column_names = df.columns.values.tolist()
    length = len(column_names)
    for i in range(length-1):
        first_word = column_names[i]
        for j in range (i+1, length):
            second_word = column_names[j]
            first_word_split = first_word.split("_")
            second_word_split = second_word.split("_")
            for k in first_word_split:
                if k in second_word:
                    return True, ""
            for l in second_word_split:
                if l in first_word: 
                    return True, ""
    return False, "Column fields of the table cannot be linked"

b14_linked_columns(df)
```


```python
#2b-15
#Can the data tables be linked to the metadata via common fields (columns)?
def b15_columns_linked_to_metadata(df, meta_dict):
    column_names = df.columns.values.tolist()
    column_names = [name.lower() for name in column_names]
    metakeys = list(meta_dict.keys())
    metakeys = [name.lower() for name in metakeys]
    
    error_message = ""
    result = True 
    for column in metakeys:
        if column not in column_names:
            result = False
            error_message += (column + " should be a column in our dataframe, ")
    return result, error_message

meta_dict ={
    "scenario": "Year (2012 to 2017)",
    "goal": "Code for the OHI goal (10 goals, represented by 2 characters; and 8 subgoals, represented by 3 characters), see table below for details",
    "long_goal": "Full goal/subgoal name",
    "dimension" : "Dimension of the OHI score that is being calculated, see table below for details",
    "region_id" : "Numeric identifier of the global region, includes countries and territories",
    "region_name" : "Name of the global region",
    "value" : "Scores ranging from 0-1 for all dimensions except trend, which is between -1 and 1"
}
b15_columns_linked_to_metadata(df, meta_dict)
```


```python
#b16-17-18
# Are the filenames consistent, descriptive, and informative (clearly indicates content) to humans?
# Do the filenames follow the convention: less than 70 characters; most unique content at start of filename; no acronyms; no jargon; no organization name?
# Was a logical, documented naming convention used for file names?


import re

def check_word_exists(split_list):
    d = enchant.Dict("en_US")
    for word in split_list:
        if d.check(word):
            return True

def b16_17_18_filename(filename):
    
    #check length of filename
    if len(filename) > 70:
        return False, "Filename is longer than 70 characters"
    
    #check for abbreviations
    caps_in_row = 0
    for char in filename:
        if char.isupper():
            caps_in_row += 1
        else:
            caps_in_row = 0
        if caps_in_row >= 3:
            return False, "No abbreviations allowed in filename"
    
    #check if there are any special characters
    if re.search("[^a-zA-Z0-9]+", Numb) == False:
        return False, "No special Characters allowed in filename"
    
    return True, ""

b16_17_18_filename("OHIDataSet.csv")
```


```python
# 2b-3 In the case where the data do not reside in a relational database, are the data files tabular?


```


```python
# 2b-5 Was a logical, documented naming convention used for variables (column names)?

def b5_check_logical_coloumn_names(df):
  headers = df.head(1);
  error_message = ""
  result = True

  n = sizeof(headers); 
  for headers in headers:
    if isinstance(headers,str):
       result = True
    else:
      error_message = ("Variable coloumn names not consistent while naming")
      result = False
      return result, error_message
    
  return True, 'All coloumn headers are strings'

```


```python
# 2b-4 Are the field types (column types) used appropriate? (i.e. date field for dates, alphanumeric field for text, numerical field for numbers, etc)

def b5_check_logical_coloumn_names(df):
  headers = df.head(1);
  error_message = ""
  result = True

  n = sizeof(headers); 
  if 
  for headers in headers:
    if isinstance(headers,str):
       result = True
    else:
      error_message = ("Variable coloumn names not consistent while naming")
      result = False
      return result, error_message
    
  return True, 'All coloumn headers are strings'


def validate(date_text):
    try:
        datetime.datetime.strptime(date_text, '%Y-%m-%d')
    except ValueError:
        raise ValueError("Incorrect data format, should be YYYY-MM-DD")
```


```python
def data_fitness_test():
    df = pd.read_csv("OHIDataSet.csv")
    essential_score = 0
    valuable_score = 0
    desireable_score= 0
    error_log = ""
    #essential: b3,b4,b6-11,b13-15, max score /11    
    try:
        if b3_check_not_relational("OHIDataSet.csv"):
            essential_score += 1
        else:
          print("Test case 2b-3 failed....")
          print(b3_check_not_relational("OHIDataSet.csv")[1])
    except:
        essential_score += 1
    try:
        if b6_check_columnNames_unique("OHIDataSet.csv"):
            essential_score += 1
        else:
          print("Test case 2b-6 failed....")
          print(b6_check_columnNames_unique("OHIDataSet.csv")[1])
    except:
        essential_score += 1
    if b10_check_column_names(df)[0]:
        essential_score +=1
    else:
      print("Test case 2b-10 failed....")
      print(b10_check_column_names(df)[1])
    
    if b11_column_names_documentation(df, ["scenario","goals","long_goal",
                                             "dimension", "region_id", "region_name",
                                             "value"])[0]:
        essential_score += 1
    else:
      print("Test case 2b-11 failed....")
      print(b11_column_names_documentation(df, ["scenario","goals","long_goal",
                                             "dimension", "region_id", "region_name",
                                             "value"])[1])
        
    if b13_unique_id(df)[0]:
        essential_score +=1
    else:
      print ("Test case 2b-13 failed....")
      print (b13_unique_id(df)[1])
      
    if b14_linked_columns(df)[0]:
        essential_score += 1
    else:
      print ("Test case 2b-14 failed....")
      print (b14_linked_columns(df)[1])
     
    meta_dict ={
        "scenario": "Year (2012 to 2017)",
        "goal": "Code for the OHI goal (10 goals, represented by 2 characters; and 8 subgoals, represented by 3 characters), see table below for details",
        "long_goal": "Full goal/subgoal name",
        "dimension" : "Dimension of the OHI score that is being calculated, see table below for details",
        "region_id" : "Numeric identifier of the global region, includes countries and territories",
        "region_name" : "Name of the global region",
        "value" : "Scores ranging from 0-1 for all dimensions except trend, which is between -1 and 1"
    }
    if b15_columns_linked_to_metadata(df,meta_dict)[0]:
        essential_score +=1
    else:
      print("Test case 2b15 failed....")
      print (b15_columns_linked_to_metadata(df,meta_dict)[1])
    
    
    
    #valuable: b1,b2,b5,b12,b16,b18 max score /6
    try:
        if b1_check_json_xml("OHIDataSet.csv"):
            valuable_score += 1
        else:
          print ("Test case 2b-1 failed....")
          
    except:
        valuable_score += 1
    
    try:
        if b2_check_3nf("OHIDataSet.csv"):
            valuable_score += 1
        else:
          ("Test case 2b-3 failed....")
    except:
        valuable_score += 1
    
    try:
        if b5_check_logical_coloumn_names(df):
            valuable_score += 1
        else:
          ("Test case 2b-5 failed...")
    except:
        valuable_score += 1
    
    if b16_17_18_filename("OHIDataSet.csv"):
        valuable_score += 2
    else:
      print ("Test cases b16,17,18 failed....")
      print (b16_17_18_filename("OHIDataSet.csv")[1])
    
    if b12_categorical_data(df):
        valuable_score += 1
    else: 
      print("Test case b12 failed....")
      print (b12_categorical_data(df)[1])
    #desireable b17,b19 max score /2
    
    if b16_17_18_filename("OHIDataSet.csv"):
        desireable_score += 1
    print("\n")
    print(str(essential_score) + "/11 essential criteria, ", str(valuable_score) +"/6 valuable score, ", str(desireable_score) +"/2 desireable score")
    print("Overall: " + str(essential_score + valuable_score + desireable_score ) +"/19")
    if ((essential_score + valuable_score + desireable_score )/19 > 0.5 ):
      print ("Test Passed")
    else:
      print("Test Failed")
          
data_fitness_test()
    
```

    Test case 2b-10 failed....
     Unnamed column in Column 5, Unnamed column in Column 8,
    Test case 2b-11 failed....
    region_id should be a column in our dataframe, 
    Test case 2b-13 failed....
    There was no unique identifier
    Test case 2b15 failed....
    goal should be a column in our dataframe, region_id should be a column in our dataframe, 
    
    
    3/11 essential criteria,  6/6 valuable score,  1/2 desireable score
    Overall: 10/19
    Test Passed
    
