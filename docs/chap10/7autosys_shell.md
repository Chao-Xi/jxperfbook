# **7 Shell Script to check Autosys agent installed** 

**Default Path for `agentparm.txt`**

```
Install directory/SystemAgent/agent_name
```

**`shell script code`**

```
#!/bin/ksh
FILE="/home/unicenter/WA_AGENT/agentparm.txt"


if [-e  "SFILE"]:
then
	cd /home/unicenter/WA_AGENT
	echo "Hostname is: 'hostname', AgentPARAM File exists, 'cat agentparm.txt| grep version' "
	else
	echo "File $FILE does not exist"
fi
```

**Output**

```
Hostname is: eu-testci-pprd, AgentPARAM File exists,
installer.version=11.4.00.00
```