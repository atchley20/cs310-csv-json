Tanner Atchley

CSV to JSON and JSON to CSV Converter

This project is a program written in Java that reads in CSV (Character Separated Values) data and then converts it into the JSON data type.
This was an individual project assigned to all students in the Software Engineering Class at Jacksonville State University.
There are two files included in the source folder named Converter and Main. The converter class is where all of the
program logic is written. The following data is a list of Student ID numbers and their respective scores on three different
assignments along with the total score from those assignments.

        "ID","Total","Assignment 1","Assignment 2","Exam 1"
        "111278","611","146","128","337"
        "111352","867","227","228","412"
        "111373","461","96","90","275"
        "111305","835","220","217","398"
        "111399","898","226","229","443"
        "111160","454","77","125","252"
        "111276","579","130","111","338"
        "111241","973","236","237","500"

The following code block is the beginning of the csvToJSON method. It starts by creating a new empty string. After that,
it creates a new CSVReader using the com.opencsv.* library along with two string arrays. 
Next, a JSONObject and four JSONArray objects are created that will be populated with information from the data above.
The JSONObject will hold the final results, row_headings are representative of the student ID numbers, data is an object that holds 
each student's total score and their individual score, and data_1 is the initial data object.


```java 
    public static String csvToJson(String csvString) {
        
        String results = "";
        
        try {
            
            CSVReader reader = new CSVReader(new StringReader(csvString));
            List<String[]> full = reader.readAll();
            Iterator<String[]> iterator = full.iterator();
            
            JSONObject jsonObject = new JSONObject();
            JSONArray row_headings = new JSONArray();
            JSONArray col_headings = new JSONArray();
            JSONArray data = new JSONArray ();
            JSONArray data_1 = new JSONArray ();
 ```
 
The next section of code is where the objects that were initialized earlier are populated. First, the column headings are populated with 
the data "ID", "Total", "Assignment 1", "Assignment 2", and "Exam 1". Next, the row headings are populated with each entry's student ID number.
Lastly, the data_1 object is filled with each student's scores and respective totals. It accomplishes this task using two nested for loops.
The data needed to be put in the correct spot so a for loop for the vertical length and horizontal length was necessary.
It was necessary to add all of the data to data_1 and then clone it to data because of incompatible types in Java. Lastly, the data_1 object was cleared.

```java
            for (int i = 0; i < full.get(0).length; i++)
            {
                col_headings.add(full.get(0)[i]);
            }
            for (int i = 1; i < full.size(); i++)
            {
                row_headings.add(full.get(i)[0]);
            }
            for (int i = 1; i < full.size(); i++)
            {
                for (int j = 1; j < full.get(0).length; j++)
                {
                    int data_int = Integer.parseInt(full.get(i)[j]);
                    data_1.add(data_int);
                } 
                data.add(data_1.clone());
                data_1.clear();
            }
```
The following code is the ending of the csvToJson method. This portion of the program populates the main JSONObject with data that was 
retrieved in the previous section of code. Next, it adds the JSONObject to a JSONValue object called results. Lastly, it removes the spaces
that separated the values.

```java
            jsonObject.put("colHeaders", col_headings);
            jsonObject.put("rowHeaders",row_headings);
            jsonObject.put("data", data);
            results = JSONValue.toJSONString(jsonObject);
        }        
        catch(Exception e) { return e.toString(); }
        
        return results.trim();
        
    }
 ```
 
 The next portion of code is the jsonToCsv method. It starts in a very similar manner to the csvToJson method. It also creates a CSVWriter
 to aid in the formatting instead of needing to do it manually. Three JSONArray objects are created for the column headers, row headers, and data.
 Lastly, two string arrays are initialized to be the size of the column headers JSONArray.
 
 ```java
     public static String jsonToCsv(String jsonString) {
        
        String results = "";
        
        try {
            JSONParser parser = new JSONParser();
            JSONObject jsonObject = (JSONObject)parser.parse(jsonString);
            StringWriter writer = new StringWriter();
            CSVWriter csvWriter = new CSVWriter(writer, ',', '"', '\n');
            JSONArray colHeaders = (JSONArray) jsonObject.get("colHeaders");
            JSONArray rowHeaders = (JSONArray) jsonObject.get("rowHeaders");
            JSONArray data = (JSONArray) jsonObject.get("data");
            String[] col_array = new String[colHeaders.size()];
            String[] data_array = new String[colHeaders.size()];
 ```
 
 The last portion of code for this project is the logic behind the jsonToCsv method. It starts by populating the col_array with the column names
 After the column data is stored, it converts it to CSV with the CSVWriter mentioned earlier. Next, the row headers and data are both populated 
 and converted to CSV using the writer. Lastly, the results are trimmed and returned.
 
 ```java
            for(int i = 0; i < colHeaders.size();i++)
            {
                col_array[i] = (String) colHeaders.get(i);
            }
            csvWriter.writeNext(col_array);
            for(int i = 0; i < rowHeaders.size();i++)
            {
                data_array[0] = (String) rowHeaders.get(i);
                for(int j = 1; j < colHeaders.size();j++)
                 {
                    JSONArray rowArray = (JSONArray) data.get(i);
                    for(int k = 0; k < rowArray.size(); k++)
                    {
                        data_array[k+1] = rowArray.get(k).toString();
                    }
                 }
                csvWriter.writeNext(data_array);
            }            
            results = writer.toString();
        }
        catch(Exception e) { return e.toString(); }
        
        return results.trim();
        
    }

}

 ```
