<h1>Configuring Examples of Security Control Types</h1>

<h2>Description</h2>
In these labs, I will use several types of security controls. First, I will work with a preventive control, then a detective control. Next, I will work with a directive control and finally, a corrective control.<br>

<h2>Tools and Languages Used</h2>

- <b>PowerShell</b> 
- <b>Server Manager</b>
- <b>Event Viewer</b>

<h2>Environments Used </h2>

- <b>Windows Server 2019</b> (1809)

<h2>Steps Taken:</h2>

<p align="center">
<b>Configure and test preventive controls</b>
<br />
<br />
In this lab, I will first perform an undesirable activity. Next, I will implement a preventive control to block that activity. And finally, I will attempt the unwanted activity again to test the preventive control.
<br />
<br />
A TOOLS folder has been configured on the DC10 server. This folder is designed to hold utilities and data files that must only be accessible to domain and local administrators only. Users without an administrative rule should be prevented from viewing the share.
<br />
<br />
I signed in as 'Sam', a non-admin account on client computer 'PC10'. Within File Explorer I went to \\10.1.16.1\TOOLS, which I was allowed to access, though I shouldn't be able to as it is for admins only.
<img src="https://i.imgur.com/HyD3jPG.png" height="80%" width="80%"/>
<br />
<br />
I switched to 'DC10' and signed in as an Administrator. Then navigated to Server manager > File and Storage Services > Shares. 
<img src="https://i.imgur.com/nDRsJKW.png" height="80%" width="80%"/>
<br />
<br />
Upon opening the TOOLS share properties, I selected Permissions and customize. This showed that the Users groups was given Read & execute access. <br/>
<img src="https://i.imgur.com/8I1c0n9.png" height="80%" width="80%"/>
<br />
<br />
I disabled inheritance of the Users groups, then removed the 'Users' groups from the permission entries and the 'Everyone' group in Share.
<img src="https://i.imgur.com/qv0NPTo.png" height="80%" width="80%"/>
<br />
<br />
Back on 'PC10' as Sam, I again tried to access the \\10.1.16.1\TOOLS folder.
<br />
<br />
Now receive a Network Error message indicating I do not have permission to access.   
<img src="https://i.imgur.com/ctLB06J.png" height="80%" width="80%"/>
<br />
<br />
I signed out and back in as Jaime, an admin, who was able to access the share.
<img src="https://i.imgur.com/rGZbTD4.png" height="80%" width="80%"/>
<br />
<br />
I have successfully implemented a preventive control to block non-administrative users from accessing resources only meant for administrators.
</p>
<br />
<br />


<p align="center">
<b>Configure and test detective controls</b>
<br />
<br />
In this lab, I will first perform an activity that will not be logged. Next, I will configure logging to record that activity, and then I will perform the activity again. Finally, I will review the log to confirm the record of the activity was created.
<br />
<br />
Within PC10, I signed in as Jaime, a local admin. I opened File Explorer and navigated to the LABFILES folder, to find the 'empty' folder which I deleted.
<br />
<img src="https://i.imgur.com/e5N3SlK.png" height="80%" width="80%"/>
<br />
<br />
I opened Event Viewer > Windows Logs > Security and using the Find function searched 'empty' which told me it was not found in the logs. This tells me that folder deletion isn't being audited.
<img src="https://i.imgur.com/W9iag8s.png" height="80%" width="80%"/>
<br />
<br />
I then opened Local Security Policy > Local Policies > Audit Policy. 
<img src="https://i.imgur.com/28FQVLE.png" height="80%" width="80%"/>
<br />
<br />
Then I opened the properties of 'Audit object access' policy, where I checked 'Success' and 'Failure' for the policy to audit both.
<img src="https://i.imgur.com/h27hIxE.png" height="80%" width="80%"/>
<br />
<br />
Within the LABFILES folder's properties, under the Security tab and Advanced option, I am able to access the Auditing tab. 
<img src="https://i.imgur.com/ALNfhXI.png" height="80%" width="80%"/>
<br />
<br />
Here I can choose Add and select a principal. I chose 'Everyone' to add.
<img src="https://i.imgur.com/xMSmnLM.png" height="80%" width="80%"/>
<br />
<br />
Opening the advanced permissions, I marked the Delete subfolders and files and Delete checkboxes.
<img src="https://i.imgur.com/7nXj36b.png" height="80%" width="80%"/>
<br />
<br />
Checked 'Replace all child object auditing…'
<img src="https://i.imgur.com/vs8gFQx.png" height="80%" width="80%"/>
<br />
<br />
Back at the File Explorer in the LABFILES folder, I deleted a folder called 'pcaps'.
<img src="https://i.imgur.com/nSbD3n9.png" height="80%" width="80%"/>
<br />
<br />
In Event Viewer, I refreshed and tried Find again, this time searching for '4660' the Event ID for the event type of object deletion, which found the log for the object being deleted. 
<img src="https://i.imgur.com/mPOVtOq.png" height="80%" width="80%"/>
<br />
<br />
5 records above 4660 is 4663, which confirms the details of the file that was deleted and that the auditing was successful.
<img src="https://i.imgur.com/r7xxoaG.png" height="80%" width="80%"/>
</p>

<br />
<br />
<p align="center">
<b>Configure and test directive controls</b>
<br />
<br />
In this lab, I will configure a directive control in the form of a login warning banner. Finally, I will test this directive control.
<br />
<br />
In PC10 under Jaime's local admin account, I first will open Powershell as Admin and enter the following entry:
<b>$BannerText = "This computer system is the property of Structureality Inc. It is for authorized use only. By using this system, all users acknowledge notice of and agree to comply with the Acceptable Use Policy (AUP). Unauthorized or improper use of this system may result in administrative disciplinary action, civil charges/criminal penalties, and/or other sanctions set forth in the AUP. By continuing to use this system, you indicate your awareness of and consent to these terms and conditions. If you are physically located in the European Union, you may have additional rights per the GDPR. Visit the website gdpr-info.eu for more information."</b>
<br />
<br />
Then: 
<b>New-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\System" -Name "legalnoticecaption" -Value "Authorized Use Only" -PropertyType "String" -Force | Out-Null</b>
<br />
<br />
Lastly: 
<b>New-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\System" -Name "legalnoticetext" -Value $BannerText -PropertyType "String" -Force | Out-Null</b>
<img src="https://i.imgur.com/EHJZvpm.png" height="80%" width="80%"/>
<br />
<br />
After running these commands, I signed out and upon entering Ctrl+Alt+Del I was met with the warning banner I had set.
<img src="https://i.imgur.com/vpu9dwr.png" height="80%" width="80%"/>
</p>

<br />
<br />
<p align="center">
<b>Configure and test corrective controls</b>
<br />
<br />
In this lab, I will first use a fault injection tool to trigger the existing correct control of Windows to trigger its native corrective control protection against misbehaving applications. Next, I will create and test a custom corrective control to protect the contents of a text file.
<br />
<br />
First I opened File Explorer, and from the Quick access pane, selected SYSINTERNALS and located 'notmyfault64' to execute it.
<img src="https://i.imgur.com/I3A9T6t.png" height="80%" width="80%"/>
<br />
<br />
Chose the code overwrite option and then 'Crash'.
<img src="https://i.imgur.com/GIGyKUY.png" height="80%" width="80%"/>
<br />
<br />
This caused the system to crash and throw a BSOD. This is Window's way of protecting the system from harm. 
<img src="https://i.imgur.com/3yNDL9o.png" width="80%"/>
<br />
<br />
Next, I will create my own corrective control to simulate the correction functions of the SigVerif utility.
<br />
<br />
From Powershell, in C:\users\jaime I created a .txt file called notes. Then calculated the hash of the file and placed it in it's own .txt file. Ran an echo command to add data into the notes file. Then ran another command to calculate the hash of notes.txt and compare it to the value stored in the original .txt file. This produced an error "The file has changed. Corrective action should be initiated."
<img src="https://i.imgur.com/iH0MdLV.png" width="80%"/>
<br />
<br />
To correct the previous action, I will perform another Set-Content command. I can then run another command to again compare the notes.txt and hash.txt values. This time coming back as correct.
<img src="https://i.imgur.com/Us97iz2.png" width="80%"/>
<br />
<br />
This was a manual corrective control, but we can also automate the process:
<br />
<br />
I can run notepad calcash.ps1 to create a new notepad file, and put in a code in the document and save it.
<img src="https://i.imgur.com/oht7I0N.png" width="80%"/>
<br />
<br />
Running the calchash.ps1 in Powershell, then making another check.ps1 notepad file with new code.
<img src="https://i.imgur.com/HWvaghg.png" width="80%"/>
<br />
<br />
I again appended the contents of notes.txt, and ran the check.ps1, which automatically corrected the change.
<img src="https://i.imgur.com/AgDqFvl.png" width="80%"/>
</p>





























