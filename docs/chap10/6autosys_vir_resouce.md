# **6 Autotsys virtual resource management**

## **1 Delete a Virtual Resource** 


Syntax 

**`delete_resource: resource_name`**


**Example1** 

```
delete_resource: global_resource_name 
```

**Example2 **

```
delete_resource: machine_resource _name 
machine: machine_name 
```

## **2 Types of Virtual Resource**

**Syntax** 

`res_type: D | R | T`

 
* D - Depletable Resource 
* R - Renewable /Borrowed resource 
* T - Threshold /Sizing resource 

Note:You cannot update a resource's type using the `update_resource` subcommand. 

**Example** 

```
insert_resource: renew_res_name 
res_type: R 
amount: 10
``` 

```
insert_job: res_dependency_job 
job_type: CMD 
machine: testmachine 
command: /home/testscript.ksh 
resources: (renew_res_name, QUANTITY=5, FREE=Y) 
```


## **3 Amount attribute of Virtual Resource** 

**Syntax** 

The **amount attribute** specifies the **number of units to assign to a virtual resource**. 

**`amount:value`**

**Example** 

```
update_resource: res_test 
amount: 20 
```

**Example2** 

```
update_resource: res_test 
machine: testmachine 
amount: +5 
```

