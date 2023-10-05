
# **5 Manage Job in the Database** 

## **1 Delete a Box Job from the Database** 

The `delete_box` subcommand deletes the specified box job and all the jobs in that box from the database 


Syntax 

**`delete_box`: `box_name`**

**Example1**


**`delete_box`: Box1**


## **2 Delete a Job from the Database** 

**Syntax** 

`delete_job: job_name` 


### **Add a Job to the Database** 

The `insert_job` subcommand adds a job definition to the database. 

Syntax 

**`insert_job: job_name`** 

**Example1**


* `insert_job: job1` 
* `machine: prod` 
* `command: /home/user/job_status.ksh` 
* `job_type:cmd` 


**Example2** 

* `insert_job: file` 
* `job_type: fw` 
* `machine: prod` 
* `watch_file: "C:\myfiles\testfile"`


**Example3** 

* `insert_job: testjob`
* `job_type: box `



## **3 Update an Existing Box or Job Definition in the Database** 

Syntax 

**`update_job: job_name`**


**Example** 


```
update_job: job1 
machine: pre-prod 
```


**Original Job definition** 


```
insert_job: job1 
machine: prod 
Start_times: "04:00"
```

## **4 Automatically Delete a Job on Completion** 

The `auto_delete` attribute specifies **the number of hours to wait after a job completes**. 

**Syntax** 

`auto_delete : hours | 0 | -1`

**Example** 

```
insert_job: jobs 
job_type: CMD 
machine: prod 
command: /home/user/job_status.ksh 
auto_delete:5 
```

## **5 Define the Number of Times to Restart a Job After a Failure**

Syntax 

**`n_retrys`:attempts**


* Applicable for only application failures 
* If job is terminated then no action of `n_retrys` 
* Not applicable for network failures 
* network failures are controlled by the **MaxRestartTrys** parameter in the configuration file.


 


