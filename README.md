# SOC Automation With Wazuh, TheHive, and Shuffle

## Objective


This project/lab allowed me to learn about new SIEM platforms that I haven't worked with before. I also gained experience writing alerting rules in a SIEM environment. Furthermore, I gained experience and understanding with SOAR and how platforms like Shuffle can increase efficiency in a SOC environment. At the end of this I now have a fully functional SOC environment to practice my triage and reporting skills.

### Skills Learned


- Public and Private IP networking using Oracle VirtualBox and Oracle Cloud
- Linux server administration and configuration
- Automating responsive actions from alerts
- Building alerting rules using Wazuh
- Further understanding of API use cases
- Building workflows in Shuffle


### Languages and Utilities Used


- Wazuh
- TheHive
- Shuffle
- Oracle Cloud
- Oracle VirtualBox


### Environments


- Windows 10 Pro (22H2)
- Kali Linux 2026.1
- Ubuntu Server 24.04.4 LTS

## Steps
<img width="634" height="720" alt="Wazuh_TheHive" src="https://github.com/user-attachments/assets/8517fa5b-f02c-4395-ab44-8e9a36995ac5" />

*Network diagram*

Install Oracle VirtualBox to host all of our machines virtually
Create 1 Windows 10 host via ISO file, 2 Ubuntu Server 26.04 LTS servers, and 1 Kali Linux endpoint
Configure all endpoint IP addresses to their respective address following the network diagram

### Windows 10 Client (user:david password:***********)
<img width="407" height="244" alt="Screenshot 2026-06-20 221617" src="https://github.com/user-attachments/assets/72e82053-ec61-471b-a408-629960f1b9e8" />

*Ref 1: Ensure that you download sysmon*
<img width="993" height="563" alt="Screenshot 2026-06-20 221848" src="https://github.com/user-attachments/assets/6f1f67f3-0f96-4cd5-b410-1420a08fc6ef" />

*Ref 2: We will also download the sysmon config by Olaf Hartong*
<img width="670" height="168" alt="Screenshot 2026-06-20 222817" src="https://github.com/user-attachments/assets/ffb014ea-6982-41f0-833d-883e843929c2" />

*Ref 3: After ensuring that the config file is in the same directory as sysmon we will install sysmon with the config file using powershell*

<img width="516" height="147" alt="Screenshot 2026-06-20 222902" src="https://github.com/user-attachments/assets/d72b698d-4e5b-4827-838f-c4482f4a3a02" />

*Ref 4: Confirm installation by checking services*

<img width="430" height="467" alt="Screenshot 2026-06-25 143621" src="https://github.com/user-attachments/assets/d6395494-154c-4749-83be-f1a8257bb684" />

*Ref 5: For clarity, we will also manually configure the network configuration according to the network diagram*

### Wazuh Manager Ubuntu Server (user:davidw password:***********)
<img width="589" height="64" alt="Screenshot 2026-06-25 141217" src="https://github.com/user-attachments/assets/d0fa5692-4ded-4cd0-8b22-79c1ad9ac4ba" />

*Ref 6: Upon initial login run 'sudo apt-get update && sudo apt-get upgrade -y' to update and upgrade all of our repositories (Ubuntu)*
<img width="483" height="244" alt="Screenshot 2026-06-25 142830" src="https://github.com/user-attachments/assets/f0f92e83-4fcf-445a-a98e-809fed936869" />

*Ref 7: Using netplan in Ubuntu, Apply netplan and verify configuration*
<img width="820" height="92" alt="Screenshot 2026-06-25 152718" src="https://github.com/user-attachments/assets/356bcee9-2644-45be-a850-ee6fb297050b" />

*Ref 8: From the Wazuh website we will follow the documentation to download and run the installation assistant with (curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a)*
<img width="748" height="75" alt="Screenshot 2026-06-26 111659" src="https://github.com/user-attachments/assets/96114282-79d1-4fc7-bb6c-d86d8566ce93" />

*Ref 9: Note the user and password as we will need this later*

*After trying this myself I realized that Wazuh is only officially supported on Ubuntu 22.04 and 24.04 (this being said, I repeated all of the above steps in a new ubuntu installation of 24.04 attempt to resolve the Wazuh installation)
*It turned out to be an issue with storage settings after setting up Ubuntu. We are still sticking with 24.04
<img width="1364" height="589" alt="Screenshot 2026-06-26 120753" src="https://github.com/user-attachments/assets/54fb18f7-b5e3-4fef-8a60-65720fbda9e1" />

*Ref 10: Verify that the Wazuh Dashboard works by accessing on our analyst workstation*

### TheHive Ubuntu Server (user:davidh password:**)
Install and setup Ubuntu just like we did with the Wazuh manager server (ensure that your server has the proper hardware specifications)
We will need to install some pre-requisets as noted in TheHive documentation (Java, Cassandra, ElasticSearch)
I followed the instructions in the documentation to assist in downloading these items
After installing of these components we will need to configure options
The first option is in the Cassandra config under /etc/cassandra/cassandra.yaml

<img width="345" height="63" alt="Screenshot 2026-06-26 155725" src="https://github.com/user-attachments/assets/b0a805f4-93dd-4e50-9a5b-8275112817fc" />

*Ref 11: Edit the listen address and the rpc listen address to TheHive server's address following out network diagram*
<img width="651" height="172" alt="Screenshot 2026-06-26 155905" src="https://github.com/user-attachments/assets/dd9a71b8-7db5-451d-a56c-eace44adcd4b" />

*Ref 12: Finally for Cassandra change the seed provider address to TheHive's address*
<img width="734" height="181" alt="Screenshot 2026-06-26 160424" src="https://github.com/user-attachments/assets/81e55a7d-7a43-415c-833d-e865c2ed075d" />

*Ref 13: Now we must stop Cassandra and remove old files located under /var/lib/cassandra/\* then restart the service with systemctl start cassandra.service*

<img width="670" height="113" alt="Screenshot 2026-06-26 162034" src="https://github.com/user-attachments/assets/8b4921ba-faa4-4d7f-8c7a-cce42708b746" />

*Ref 14: After this we will configure ElasticSearch under /etc/elasticsearch/elasticsearch.yml including cluster.name, node.name, network.host, and remove node-2 from cluster initial master node*
<img width="778" height="184" alt="Screenshot 2026-06-26 162230" src="https://github.com/user-attachments/assets/2876b454-121b-4806-a23e-f7d16b502b7a" />

*Ref 15: Now we just have to start and enable elastic search using systemctl*

<img width="460" height="112" alt="Screenshot 2026-06-26 171253" src="https://github.com/user-attachments/assets/aed685be-abba-4eb5-a5d4-ea598df0b33d" />

*Ref 16: Before configuring TheHive, we must ensure that TheHive has access to the /opt/thp file path*
Now that all of the pre-requisets are done we can edit TheHive's configuration file

<img width="392" height="243" alt="Screenshot 2026-06-27 152430" src="https://github.com/user-attachments/assets/58acc12c-2a9e-4fea-ab1d-37bebfcc0f87" />

*Ref 17: We need to edit hostname address, cluster-name to (Test Cluster), and application base URL*
<img width="939" height="113" alt="Screenshot 2026-06-27 152526" src="https://github.com/user-attachments/assets/b06cb532-cffc-4a25-9723-a4eb86d413fe" />

*Ref 18: Notice how the comment for storage configuration says the path should belong to the user/group running thehive service, that's why we had to use chown earlier on the /opt/thp file path*
<img width="945" height="100" alt="Screenshot 2026-06-27 154116" src="https://github.com/user-attachments/assets/29c569b7-77d1-4806-b0e8-53ff92412d7c" />

*Ref 19: After this is done we can start and enable thehive service*
<img width="1109" height="778" alt="Screenshot 2026-06-27 154634" src="https://github.com/user-attachments/assets/d38222af-f4d3-4deb-bd3e-941454035773" />

*Ref 20: Now we can log in from our analyst workstation*

### Configuring Wazuh Agent on Windows client:
<img width="1247" height="591" alt="Screenshot 2026-06-28 170541" src="https://github.com/user-attachments/assets/7f500333-0555-4892-8027-8c9897c349cd" />

*Ref 21: Selecting deploy new agent we can configure information for the Wazuh agent service we will start on the windows client*
<img width="945" height="156" alt="Screenshot 2026-06-28 170625" src="https://github.com/user-attachments/assets/eeb03a7b-f349-4017-b819-23a944c210eb" />

*Ref 22: Copy the command provided by the dashboard and run it in powershell*

<img width="378" height="99" alt="Screenshot 2026-06-28 170821" src="https://github.com/user-attachments/assets/979faba1-0683-40ed-aa19-c6e5f422c111" />

*Ref 23: Then run net start wazuhsvc*
<img width="1466" height="296" alt="Screenshot 2026-06-28 170857" src="https://github.com/user-attachments/assets/48154559-f135-409c-b30a-3bb334908fda" />

*Ref 24: After refreshing the dashboard we can see that the agent is now active*

### Generating alerts in Wazuh with mimikatz
Now that everything is up and running we will generate some alerts with mimikatz on our windows 10 client to test out functionality and gain further experience
First we will edit the ossec.conf files on the windows machine to allow Wazuh to ingest sysmon logs
<img width="598" height="121" alt="Screenshot 2026-06-30 171136" src="https://github.com/user-attachments/assets/845e1200-1c18-44a3-9cf2-1e05b6ee4acb" />

*Ref 25: After making a backup of the conf file, we will make a new localfile tag and change the location tag within it to the sysmon channel name*
<img width="645" height="585" alt="Screenshot 2026-06-30 171154" src="https://github.com/user-attachments/assets/79a512ec-3639-4098-8742-97fb008f4c85" />

*Ref 26: We find this by going to sysmon in event viewer and checking the operational properties*
<img width="449" height="344" alt="Screenshot 2026-06-30 172039" src="https://github.com/user-attachments/assets/464cab6c-db5d-401f-b4e2-6c49b17b9bbf" />

*Ref 27: After this save the .conf file and restart the Wazuh service*
<img width="577" height="298" alt="Screenshot 2026-07-01 152548" src="https://github.com/user-attachments/assets/3e8ac628-15d9-4b73-a53f-379c5f2f20c6" />

*Ref 28: For this next step we will add a folder exclusion for wherever we choose to download mimikatz, we may also need to disable some security features on our browser to allow the download to take place*

<img width="562" height="203" alt="Screenshot 2026-07-01 161640" src="https://github.com/user-attachments/assets/c779de11-eac8-40b9-b056-bb20c6219b43" />

*Ref 29: Now we can run mimikatz in powershell and check Wazuh for events*
After searching Wazuh for mimikatz we notice that there are no events triggered by specific alerting rules to recognize mimikatz

<img width="436" height="144" alt="Screenshot 2026-07-01 171110" src="https://github.com/user-attachments/assets/d57eb39d-fad3-41e2-8083-da4d5248ec0f" />

*Ref 30: Let's change the Wazuh manager to log everything in the json format by editing the ossec.conf file on the Wazuh manager server*

Now let's restart the Wazuh manager service (systemctl restart wazuh-manager.service)
Now all of the logs created will be archived and put into a file under /var/ossec/logs/archives (archives.json and archives.log)

<img width="183" height="116" alt="Screenshot 2026-07-02 151356" src="https://github.com/user-attachments/assets/167bc904-f98c-4880-be61-656f73b5faac" />

*Ref 31: Next to ingest these logs into the Wazuh dashboard we need to edit the filebeat configuration to enable archives*

Now restart filebeat
In the Wazuh dashboard go to dashboard management to create an index for archives
With the index pattern wazuh-archives-**

<img width="496" height="235" alt="Screenshot 2026-07-02 215605" src="https://github.com/user-attachments/assets/10828271-ebd5-4dea-a1e7-67baeec1abdd" />

*Ref 32: In the time field we will select timestamp*
<img width="927" height="208" alt="Screenshot 2026-07-02 220558" src="https://github.com/user-attachments/assets/aef079fe-0412-46db-bf6c-c9ca764b488b" />

*Ref 33: Now from the archives index we can view the log for sysmon eventid 1 that contains information regarding mimikatz*

### Creating alert for mimikatz using originalFileName field
Under server management select rules
<img width="807" height="298" alt="Screenshot 2026-07-02 222525" src="https://github.com/user-attachments/assets/c6fdd111-28c3-4bfc-8146-ed4811d92629" />

*Ref 34: Selecting manage rules we can search for sysmon*
<img width="863" height="208" alt="Screenshot 2026-07-02 222718" src="https://github.com/user-attachments/assets/af024611-b948-4e35-891f-9ee43989045b" />

*Ref 35: We will use this rule as a reference to create our own rule*
<img width="781" height="185" alt="Screenshot 2026-07-02 231100" src="https://github.com/user-attachments/assets/7a947723-89c0-4467-98a0-d9c23bff54ef" />

*Ref 36: Adding the new rule to local_rules file we will start with rule id 100002, set the level to the max of 15, change field to originalFileName, changing field value to mimikatz, remove options: no_full_log, change description to something fitting, and finally change mitre id to T1003(credential dumping)*
After this simply restart the Wazuh manager service
<img width="568" height="162" alt="Screenshot 2026-07-02 232622" src="https://github.com/user-attachments/assets/a677d394-51da-47f1-9ac1-94b7740acae5" />

*Ref 37: Now just to prove a point we will rename mimikatz file name and run it*
<img width="979" height="169" alt="Screenshot 2026-07-02 232657" src="https://github.com/user-attachments/assets/696740ca-6c7d-4b45-bdc5-b5ef0138d2ae" />

*Ref 38: When we refresh we can see that our alert has triggered*

### Implementing SOAR with Shuffle and TheHive for case management
Now we will be implementing SOAR functionality by combining Wazuh, TheHive, and Shuffle
Under workflows we will select create workflow
<img width="325" height="305" alt="Screenshot 2026-07-05 175633" src="https://github.com/user-attachments/assets/e20b4ba1-f0b8-4934-a688-007eebb99917" />

*Ref 39: We will start by creating a Wazuh Webhook making sure to copy the webhook uri*
<img width="866" height="122" alt="Screenshot 2026-07-05 181946" src="https://github.com/user-attachments/assets/2321a611-dc1d-43e1-8798-1eb7287559dd" />

*Ref 40: Now add an integration tag in the ossec.conf file on the Wazuh manager*
Change the level tag to rule_id and set it to the rule we created in Wazuh for mimikatz earlier (100002)
Save the file and restart the Wazuh manager service
After running mimikatz on our windows machine let's verify that Wazuh events are being pushed to Shuffle
<img width="809" height="506" alt="Screenshot 2026-07-05 182919" src="https://github.com/user-attachments/assets/c1ea1c6b-0325-4983-b5a5-80ff9b6c3180" />

*Ref 41: Under execution arguments we can see the event is being ingested*
From here we will begin to build useful workflows, first we will extract the mimikatz file hash from the Wazuh alert, then we will use automation with Shuffle to check file hash reputation on virustotal, after this details will be sent to thehive for the soc analyst to work through

<img width="358" height="465" alt="Screenshot 2026-07-06 171212" src="https://github.com/user-attachments/assets/edb6434a-d801-481b-9801-ab91810d65a8" />

*Ref 42: First we will use the get file meta action to extract the SHA256 hash*
After this we will select the virustotal app and enter our api key, setting get hash report action, then specify the sha-256 hash list generated by our Shuffle action
<img width="447" height="484" alt="Screenshot 2026-07-06 194244" src="https://github.com/user-attachments/assets/9a820e88-d395-4db4-b95c-a052e9236d34" />

*Ref 43: After testing the workflow we can see that everything is running smoothly so far*

(It was at this point that I realized I made a mistake with my thehive and Wazuh management servers, I need these servers to be reachable publically via port forwarding and NAT. I decided to recreate these servers in Oracle's free cloud service because I don't want to change the settings on my home's router. I will leave the information above as configuring these servers is largely the same other than using public IP addresses and working with Oracle Cloud instances)
Now to integrate thehive we need to go thehive web interface and create an account specifically for this automation

<img width="668" height="577" alt="Screenshot 2026-07-06 214048" src="https://github.com/user-attachments/assets/3c5b97e7-28b0-4f00-a128-d47fba958fd1" />

*Ref 44: First we will create a new organisation within thehive I called it (SOC Automation workflow)*
Next we will create two users
<img width="664" height="451" alt="Screenshot 2026-07-06 220357" src="https://github.com/user-attachments/assets/ffd2dc6c-6b9d-4594-9e38-dd8211386e9e" />

*Ref 45: The first one will be a normal account*

<img width="665" height="401" alt="Screenshot 2026-07-06 220547" src="https://github.com/user-attachments/assets/3986a30f-dc45-45b7-9016-9e633cad5848" />

*Ref 46: The next one will be a service account that Shuffle will use*
For the normal account ensure that you create a password so we can log in later
On the service account we will create an API key

<img width="479" height="529" alt="Screenshot 2026-07-12 162504" src="https://github.com/user-attachments/assets/72c15017-70fd-45bf-bdd7-cd5430dfbfc1" />

*Ref 47: Back in Shuffle we will use this api key to authenticate as well as our public address*

<img width="202" height="192" alt="Screenshot 2026-07-12 162817" src="https://github.com/user-attachments/assets/ab4acae1-32fa-4773-8f37-b44f8efcf0eb" /> <img width="308" height="133" alt="Screenshot 2026-07-12 163106" src="https://github.com/user-attachments/assets/d94fb4dc-8587-40ad-9b39-37d0478130a0" /> <img width="293" height="501" alt="Screenshot 2026-07-12 163956" src="https://github.com/user-attachments/assets/acf1c143-b705-493e-893f-356c862d1ed4" />

*Ref 48: In thehive application in Shuffle under find actions we will select create alert, set a description, change the date field, set the flag as false, set pap as 2 which is default, set severity, source, sourceref, status, summary, add the MITRE ATT&CK tag, traffic light protocol as 2,  and set the type as internal*
<img width="1155" height="422" alt="Screenshot 2026-07-12 164140" src="https://github.com/user-attachments/assets/35825ee2-8a25-4de5-ae04-c5fb3dd0af99" />

*Ref 49: Now if we run the workflow we can see that the alert has pushed to our thehive dashboard*
We can also add an email function to Shuffle so that it sends an alert to the analyst's email setting recipient, subject, and a body

### Setting up responsive actions in Shuffle with Wazuh:
To showcase this responsive action I will create an Ubuntu machine to use the responsive scripts on (We will need to create this machine, add an agent to it, create a new Wazuh rule to identify icmp, restart the Wazuh manager, and create a webhook within Shuffle using integration api based on our new Wazuh rule)

<img width="430" height="550" alt="Screenshot 2026-07-14 153210" src="https://github.com/user-attachments/assets/53c1612e-ed1c-4fa9-a04e-de4f8124b914" />

*Ref 50: We will also need to change our virustotal application to respond with enrichment on the srcip from the IP that we ping (get an IP address report)*
We will set up a responsive action that blocks icmp replies by automatically modifying the iptables
the Shuffle workflow will be the same with a few additions to allow responsive action

<img width="431" height="415" alt="Screenshot 2026-07-13 104906" src="https://github.com/user-attachments/assets/fe34787c-e12e-487c-bf8e-4e745de1fe7a" />

*Ref 51: First we will add the http application with the curl action selected*
<img width="600" height="173" alt="Screenshot 2026-07-13 105143" src="https://github.com/user-attachments/assets/99349dc6-72cd-4c96-b178-fe27ece32b82" />

*Ref 52: In the statement box we will put 'curl -u USER:PASSWORD -k -X "https://WAZUH-IP:55000/security/user/authentication?raw=true"' (In USER:PASSWORD use the user and password for the Wazuh api account, also use the ip address For your Wazuh management server in place of WAZUH-IP)*

This will allow Shuffle to utilize Wazuh's api capability by authenticating with a jwt token.
Shuffle will use the Wazuh api user account to do this

<img width="445" height="321" alt="Screenshot 2026-07-13 111639" src="https://github.com/user-attachments/assets/bb8d16e6-efc8-4aab-a0fd-85cc212b57ae" /> <img width="454" height="302" alt="Screenshot 2026-07-13 112152" src="https://github.com/user-attachments/assets/b101adda-c761-4780-87c7-e83c5c50cd47" />

*Ref 53: Next we will select the Wazuh application, use the run command action, set apikey as Get-API, set the url to your Wazuh IP, change agent list to your machines specific agent ID as shown in the Wazuh dashboard, set wait for complete to true*

Before we configure alert, arguments, command, and custom, we will need to set up active response in our Wazuh manager
<img width="1033" height="270" alt="Screenshot 2026-07-13 113558" src="https://github.com/user-attachments/assets/0f85f097-381a-4108-98bd-3f9a1f4a4f0d" />

*Ref 54: In the ossec.conf file under the active response section we will utilize the firewall-drop command*
<img width="857" height="281" alt="Screenshot 2026-07-13 113920" src="https://github.com/user-attachments/assets/d3991d65-a050-4211-b23b-437c77b1edcf" />

*Ref 55: In active-response options we will set several tags including command, location, level (level of the Wazuh alert), and timeout*
After changing the config file we must restart the Wazuh manager service
We can find the response name that we must know to ulitilize the Wazuh api by using the ./agent_control binary located at /var/ossec/bin with -L for list available active responses
<img width="1361" height="161" alt="Screenshot 2026-07-13 114511" src="https://github.com/user-attachments/assets/a77110ed-9c08-4768-85c2-237cdb8af9ea" />

*Ref 56: We can see the response name as firewall-drop0*
<img width="1292" height="72" alt="Screenshot 2026-07-13 132211" src="https://github.com/user-attachments/assets/886e4f51-da79-497f-b4d9-5f8f61c3e2fb" />

*Ref 57: To test out this responsive action run ./agent_control -b 8.8.8.8 -f firewall-drop0 -u 002*
Now on our ubuntu machine if we try to ping 8.8.8.8 we can see that the requests do not go through
<img width="1295" height="156" alt="Screenshot 2026-07-13 132521" src="https://github.com/user-attachments/assets/7faa0019-34b7-430c-81d3-75ef12be80f0" />

*Ref 58: If we check IPtables we can see that google dns is being dropped*
We can also audit this response by viewing /var/ossec/logs/active-response.log

<img width="529" height="529" alt="Screenshot 2026-07-13 135017" src="https://github.com/user-attachments/assets/a53bc598-c34d-408d-83f6-7459f051c82d" /> <img width="416" height="478" alt="Screenshot 2026-07-14 192530" src="https://github.com/user-attachments/assets/1829f7da-cf94-4cda-b5a5-771cca02240d" />

*Ref 59: Back in Shuffle within the Wazuh application we will set the command to firewall-drop0, we will also set the alert section with a dynamic source ip by writing {"data":{"srcip":"$exec.all_fields.data.srcip"}} (this can be easily created by selecting fields from execution arguments)*
<img width="428" height="375" alt="Screenshot 2026-07-14 153613" src="https://github.com/user-attachments/assets/37b02d9a-fc00-40df-aed7-ff1c2b2392ef" />

*Ref 60: Next we will add a user input to our workflow that asks the user via email if they would like to block the srcip based on the existing execution argument*

<img width="476" height="92" alt="Screenshot 2026-07-14 155545" src="https://github.com/user-attachments/assets/6ecf2634-9107-4320-a421-9ff03ba6b3a4" /> <img width="397" height="184" alt="Screenshot 2026-07-14 155640" src="https://github.com/user-attachments/assets/aa004929-85de-4140-a154-3ab110be18c7" />

*Ref 61: Running this workflow and attempting to ping the ubuntu machine we can check our analyst temp email*
<img width="1270" height="301" alt="Screenshot 2026-07-14 155851" src="https://github.com/user-attachments/assets/9e89ee03-c428-4b1d-99c5-4c45fd1a3f3e" />

*Ref 62: Checking our IPtables we can see that the active response has worked*
<img width="1191" height="250" alt="Screenshot 2026-07-14 155956" src="https://github.com/user-attachments/assets/2a98895b-679b-4e68-a409-b52b67cfa21b" />

*Ref 63: The final workflow*






















