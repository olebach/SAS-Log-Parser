# SAS-Log-Parser

The SAS log parser analyzes SAS codes by breaking them up into constituent components (data- and proc steps) and analyzing their structure, execution time and logic, which is then presented in the form of a dashboard.

## Setup

In order to sucessfully launch the SAS Log Parser, the user has to perform the two steps:
1. Prepare the log for the SAS code
2. Run the Jupyter notebook


### 1. Preparing the log

Unfortunately, the standard log outputted by SAS is **not suited** for analysis as it lacks key information about the process. Instead, the user can prepare the log with the help of two PROC SCAPROC procedures (record and write) placed before and after the code:

```{sas}
/* SET PATH TO SAVE THE SCAPROC PROCEDURE LOG FILE*/
%let path = path_to_store_txt_file;
proc scaproc; 
   record "&path./record.txt" OPENTIMES EXPANDMACROS; 
run;

/* SAS CODE SHOULD BE PLACED HERE */

/* WRITE LOG TO THE FILE IN PRIEVIOUSLY STATED FILE PATCH */
proc scaproc; 
   write; 
run;
```

This log will contain information about input and output tables, process execution times and the use of macro symbols in the SAS code. An example of a sample code as well as the resulting log is availiable is includer in this repository.

### 2. Jupyter notebook

Once the log is obtained, the user can launch the notebook in order to visualise the data contained in the log. In particular, the notebook has the following structure:

1) **The Log parsing** section extracts the information from the txt file and prepares 3 tables: 
    - df_log contains information on all the actions performed by the SAS process.
    - df_code is a short summary of the SAS code (code syntax, exection time, procedure used, etc. 
    - df_connections contains the information about all tables used in the process and their relationships

2) **The Graph Data** section generates a networkx Graph and performs network analysis on the table connections.

3) **The Dash plot** section creates all components necessary for the dashboard visualisation (network plot, table, interactions, etc.) and launches a local Dash server for the dashboard under <http://127.0.0.1:8050/>


### Dashboard features
The obtained dashboard has 3 functionalities encoded for performing SAS code analysis:

1) **Table filtering** - dash tables support filtering operations, e.g. comparison operators (>, gt, <=, le), string search (contains <table_name>) or multiple conditions ( {Task ID} = 1 or {Task ID} = 10). Once the data is filtered, the data in the network graph will adjust accordingly to highlight corresponding tables and connections. For more information on filter queries please consult <https://dash.plotly.com/datatable/filtering>.

2) **Network selection** - the plotly graph has multiple operations availiable for plot view manipulation - zoom, pan, autoscale, etc. Additionally, if the user uses the select tool (Box select or Lasso select) to filter tables for analysis, the table will update in order to show the corresponding codes.

3) **The Reset button** reverts the table and network plot to their initial state

