# SOC_Automation_Project_Home_Lab
The goal of this home lab is to mimic a SOC environment for identification, containment and eradication of possible threats, by sending alerts to a SOAR platform and emailing the analyst about the details of the alert. This home lab will also showcase some automation and responsive capabilities. This project will include Wazuh as an SIEM & XDR, TheHive for case management and Shuffle Automation for SOAR capabilities. This lab will be divided in 4 parts: 
1. Logical Diagram
2. Tools Configuration
3. Telemetry Generation
4. SOAR Setup

# Part 1
Here is an explanation of the logical diagram:
![Screenshot 2024-12-24 182824](https://github.com/user-attachments/assets/cd4660dd-dece-4592-bde1-0f1c950d9d5b)

1. The Wazuh agent send events to the internet.
2. Internet "sends" the events to the Wazuh manager which will create events.
3. The Wazuh manager sends the alerts to Shuffle.
4. Shuffle will gather information about the alerts and enrich the indicators of compromise (IOCs).
5. Shuffle will send the alerts to TheHive.
6-7. Shuffle will also send an email containing information about the events to the SOC analyst using internet.
8. If an action is required, the SOC analyst will send the informations to the Windows 10 client through internet, Shuffle, and Wazuh Manager.

# Part 2
Here I have 3 instances:
-A Windows 10 instance running under Virtual Box in which Sysmon is installed.
![Screenshot 2024-12-26 172543](https://github.com/user-attachments/assets/e6fec1c4-12ec-4b07-a9f3-b12dc14fea06)

-A Wazuh instance running in the cloud on Digital Ocean on a specific IP address. After some configuration, I was able to add the windows instance as an agent.
![Screenshot 2024-12-26 173002](https://github.com/user-attachments/assets/2181d3b3-d0a8-4173-8afa-99d8bc8060c0)

-A TheHive instance also running in the cloud on Digital Ocean in which Java, Cassandra, and ElasticSearch are installed.
![Screenshot 2024-12-26 173152](https://github.com/user-attachments/assets/311db664-e675-48b6-aa22-cfdafd314f1f)

# Part 3
The goal on this part is to generate telemetry to see if we can see on Wazuh if an alert will happen for a specific event. This specific event will be the usage of mimikatz which is basically a tool that is used to extract credential from the windows memory of a device. So after some configuration on Wazuh so it can ingest telemetry coming from Sysmon, mimikatz was used on the device and the goal was to see what kind of telemetry was generated on Wazuh, so that I could create a custom alert for this type of event. I decided to create a custom alert on Wazuh when Sysmon would detect an event with an original file being mimikatz.exe
![Screenshot 2024-12-26 214029](https://github.com/user-attachments/assets/b911ce06-d5ca-4b38-a4f7-0f894bfdb082)
After creating the alert, I re-used mimikatz, but this time having it named different to see if the Wazuh would still alert us since the original file name is the same. And it did!
![Screenshot 2024-12-26 215312](https://github.com/user-attachments/assets/395589c0-eac2-49e7-a0f5-b6e63a0fe89f)
