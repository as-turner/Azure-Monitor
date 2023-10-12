# Azure Monitor

![Azure Monitor(1)](https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/c1860eb5-ced4-423e-aa30-88f1a2cf15b7)


## Collect data from an Azure virtual machine with Azure Monitor

Duration: 20 minutes

## Objective
In this exercise, I will collect data from an Azure virtual machine using Azure Monitor. I will deploy a virtual machine, create a Log Analytics workspace, enable the Log Analytics virtual machine extension, configure data collection, and view/query the collected data.

Prerequisites:

- An Azure subscription with Owner or Contributor role access.
- Basic knowledge of Azure virtual machines and Azure portal.

<details> 
  
<summary> 
  
### Task 1: Deploy an Azure virtual machine
  
  </summary>   

Sign in to the Azure portal at https://portal.azure.com/ using an account with Owner or Contributor role access to the Azure subscription.

Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select PowerShell and Create storage.

Ensure PowerShell is selected in the drop-down menu in the upper-left corner of the Cloud Shell pane.

Run the following command in the PowerShell session within the Cloud Shell pane to create a resource group that will be used in this lab:

```powershell
New-AzResourceGroup -Name AZ500LAB131415 -Location 'South Central US'
````

Wait for the resource group creation to complete.

Run the following command in the PowerShell session within the Cloud Shell pane to create a new Azure virtual machine:

```powershell
New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2
```

When prompted for credentials, use the following:

|User: | localadmin|
|------|------|
| Password: | Use a password that can be remembered easily|
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/fcd85178-a242-428e-840c-3db5e89ff5bd" height="80%" width="80%" alt="Create Resource Group and Virtual Machine in PS1(2)"/>
  

Wait for the virtual machine deployment to complete.

Run the following command to confirm that the virtual machine named "myVM" was created and its ProvisioningState is "Succeeded":

```powershell
Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
```

<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/939cb4b4-595e-4e85-9142-fabfbfa313cc" height="80%" width="80%" alt="Successfully Deployed VM"/>
  
  
Close the Cloud Shell pane.
  
</details>

#

<details> 
  
<summary> 

### Task 2: Create a Log Analytics workspace
  
  </summary>   

In the Azure portal, use the search box at the top to search for "Log Analytics workspaces" and press Enter.

On the Log Analytics workspaces blade, click "+ Create."

On the Basics tab of the Create Log Analytics workspace blade, specify the following settings:

| Subscription: | Select the name of the Azure subscription you are using in this lab.|
|--------|-------|
| Resource group: | AZ500LAB131415|
| Name: | Enter any valid, globally unique name for the Log Analytics workspace.|
| Region: | South Central US|
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/e2811af8-4f3f-414c-8343-446c7a03bad3" height="50%" width="50%" alt="Create Log Analytics Workspace"/>
  

Select "Review + create."

On the Review + create tab of the Create Log Analytics workspace blade, click "Create" to start the workspace creation.

Wait for the Log Analytics workspace creation to complete.

</details> 

#

<details> 

<summary> 
  
### Task 3: Enable the Log Analytics virtual machine extension
  
</summary>  

In the Azure portal, navigate back to the Log Analytics workspaces blade.

In the list of workspaces, click the entry representing the workspace you created in the previous task.

On the Log Analytics workspace blade, on the Overview page, in the "Connect a Data Source" section, click the "Azure Virtual machines (VMs)" entry.
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/c98d3f03-922f-4754-915f-db477aaea4ae" height="70%" width="70%" alt="Connect a Data Source (VM)"/>
  

  >**Note**: For the agent to be successfully installed, the virtual machine must be running.

In the list of virtual machines, locate the entry representing the Azure VM "myVM" you deployed in Task 1 and note that it is listed as "Not connected."

Click the "myVM" entry and then, on the "myVM" blade, click "Connect."
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/5061f334-44b3-41fe-9821-02446b8eaff1" height="50%" width="50%" alt="Connect VM to LAW"/>
  

Wait for the virtual machine to connect to the Log Analytics workspace. The status displayed on the "myVM" blade will change from "Connecting" to "This workspace."
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/be9c6b5e-3a4a-45cc-a2ad-37fe2a7c1d7b" height="40%" width="40%" alt="Connected VM to LAW"/>
  
  >**Note**: This may take a few minutes. The Status displayed on the myVM blade, will change from Connecting to This workspace.  
  
</details> 

#

<details> 
  
<summary>   

### Task 4: Collect virtual machine event and performance data
  
  </summary>   

In the Azure portal, navigate back to the Log Analytics workspace you created earlier in this exercise.

On the Log Analytics workspace blade, in the "Classic" section, click "Legacy agents management."

On the Agents configuration blade, review the configurable settings including Windows Event Logs, Windows Performance Counters, Linux Performance Counters, IIS Logs, and Syslog.

Ensure that "Windows Event Logs" is selected, click "+ Add windows event log," and in the listing of event log types, select "System."
 
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/7aa92297-42ad-4b16-8230-8f01a19647dc" height="80%" width="80%" alt="Add Windows Event Logs"/>  
 
  >**Note**: This is how you add event logs to the workspace. Other choices include, for example, Hardware events or Key Management Service.  

Deselect the "Information" checkbox, leaving the "Error" and "Warning" checkboxes selected.

Click "Windows Performance Counters," click "+ Add performance counter," review the listing of available performance counters, and add the following ones:

- Memory(*)\Available Memory Mbytes
- Process(*)% Processor Time
- Event Tracing for Windows\Total Memory Usage — Non-Paged Pool
- Event Tracing for Windows\Total Memory Usage — Paged Pool

  >**Note**: The counters are added and configured with a 60-second collection sample interval.

On the Agents configuration blade, click "Apply."
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/c5831563-6c27-4e0c-bc10-dc193afc18b1" height="80%" width="80%" alt="Add Performance Counter"/>
  
</details>  

#

<details> 

<summary> 

### Task 5: View and query collected data
  
</summary>   

In the Azure portal, navigate back to the Log Analytics workspace you created earlier in this exercise.

On the Log Analytics workspace blade, in the "General" section, click "Logs."

If needed, close the "Welcome to Log Analysis" window.

On the Queries pane, in the "All Queries" column, scroll down to the bottom of the list of resource types, and click "Azure Monitor."

Review the list of predefined queries, select "Memory and CPU usage," and click the corresponding "Run" button.
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/2bfb1374-ca95-408e-a9e4-d401896560b3" height="90%" width="90%" alt="Run Memory and CPU Usage"/>
  

  >**Note**: You can start with the query "Virtual machine available memory." If you don't get any results, check that the scope is set to "virtual machine."

The query will automatically open in a new query tab. Note: Log Analytics uses the Kusto query language. You can customize the existing queries or create your own.
  
<img src="https://github.com/0xbythesecond/Azure-Monitor/assets/23303634/4381c4fe-72e8-4000-9c4a-daf9f5fae734" height="70%" width="70%" alt="Query Results for Memory and CPU Usage"/>
  
Kusto Query Language (KQL) that was run for the Memory and CPU Usage:
  
```kql
// Memory and CPU usage 
// Chart all computers' used memory and CPU, over the last hour. 
Perf
| where TimeGenerated > ago(1h)
| where (CounterName == "% Processor Time" and InstanceName == "_Total") or CounterName == "% Used Memory"
| project TimeGenerated, CounterName, CounterValue
| summarize avg(CounterValue) by CounterName, bin(TimeGenerated, 1m)
| render timechart
```  
  

  >**Note**: The results of the query you selected are automatically displayed below the query pane. To re-run the query, click "Run."

  >**Note**: Since this virtual machine was just created, there may not be any data yet.

  >**Note**: You have the option of displaying data in different formats. You also have the option of creating an alert rule based on the results of the query.

Optional: Generate additional load on the Azure VM to collect more data:

Navigate to the Azure VM blade.
In the Operations section, select "Run command."
On the "RunPowerShellScript" blade, run the following script:

```elm
cmd
:loop
dir c:\ /s > SWAP
goto loop
```  
  
Switch back to the Log Analytics blade and re-run the query. You might need to wait a few minutes for data to be collected before re-running the query.

</details>   

## Reflection
This exercise provided hands-on experience with Azure Monitor and Log Analytics for data collection and analysis. It familiarized me with deploying virtual machines, creating workspaces, connecting virtual machines to workspaces, configuring data collection settings, and running queries for analysis. It emphasized the significance of monitoring data to optimize performance and make informed decisions.  
  
## Conclusion  
This exercise demonstrated the process of collecting data from an Azure virtual machine using Azure Monitor and Log Analytics. We deployed a virtual machine, created a Log Analytics workspace, enabled the Log Analytics virtual machine extension, configured data collection settings, and viewed/queried the collected data. It highlighted the importance of monitoring and analyzing data to gain insights into virtual machine performance and behavior.  

<p align="right"> Continue to <a href="https://github.com/0xbythesecond/Microsoft-Defender-for-Cloud">Microsoft Defender for Cloud</a></p>




