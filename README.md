# Analyzing PCAPs with Wireshark 

## Objective
Analyze PCAPs using Wireshark and identify suspicious or malicious activity, while collecting important information from the observed network traffic. 

Investigation Scenarios
 
PCAP 1 -A suspicious file has been downloaded onto one of our systems. You need to export it from the PCAP and collect indicators such as the file name, size, and hash values so we can perform a threat hunt to identify if it is present on any other systems within the organization.
PCAP 2 -One of our File Transfer Protocol (FTP) servers is under attack! It's your job to analyze the captured network traffic to determine whether the attacker was successful.

### Skills Learned
- Identifying suspicious or malicious activity using Wireshark.

# PCAP 1 

## A Sr. Analyst has asked to investigate a .zip file that was downloaded. The most likely protocol is going to be HTTP, so we'll go to File > Export Objects > HTTP. Looking at the window we can see there is a ZIP file based on the Content-Type column showing ‘application/zip’, the filename is r4ckx0r.zip. If the ZIP didn't show up here, we would go to File > Export Objects, and look at the other options such as SMB, SFTP, etc. 


![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/a22c48ff-ff93-45ec-ae53-aca84f9d0753)

## Selecting the row with the .zip will take us to the correct packet within Wireshark. We can then right-click the packet and select Follow > TCP Stream. We can now see the red section that shows the GET request from the client at 192.168.56.1, and the blue section that shows the OK 200 response from the server at 192.168.56.111, and then the file contents are transferred to the client.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/97f3baaa-5e61-4dc7-b049-9cc8d818a1b6)

## We can also get more information like the source port (server) and destination port (client) in the TCP layer of the packet header section.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/91104617-745e-46f6-8c17-8b5e6c9b72a9)

## Using File > Export > HTTP, then saving cr4ckx0r.zip to the Desktop we can open a terminal, use bash to get a bash shell, and then run md5sum cr4ckx0r.zip to get the hash value. We can use this hash to analyze the file further.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/1a75fe18-0316-458a-aa2e-4bd340d237ed)

## We can double-click the zipped file to view the contents inside. Do this on an isolated PC used for digital forensics to be safe. We can see that the file inside is named ‘hashcat', a popular offensive/auditing tool used to crack hashes and passwords to reveal the plaintext versions. We can get the Hash of that file and perform OSINT on it to gather more information on the application.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/ab7b4cee-bd24-4b64-8c6a-a351527bf2b4)

## We can then document all our findings and proceed to the eradication and remediation stage.

# PCAP 2

## Since we were informed that the FTP server is under attack, we can filter this PCAP for only FTP traffic to remove any unrelated noise. 

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/9c8d9e49-759e-45da-b25a-e322999f2e82)

## When filtering for ftp packets only we see in the "info" column that many of these packets contain "Response: 220" codes. We can see what these codes mean by expanding the FTP section in the packet header fields.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/a8e33d7e-df00-4a3e-836a-8c08394a7a9c)

## So now we know that this packet represents the FTP server sending a response message to a client that is attempting to initiate a connection. From this information, we know that the source IP must be the server because it is sending responses to requests. The FTP server IP is 192.168.56.118.

## A Sr. Analyst has informed us that a dictionary attack is taking place and wants us to find when the first password was sent against the FTP server. Scrolling down the packets with our ftp filter still applied, we'll come across packets that contain ‘PASS’ followed by a value, which represents a password submitted to the server.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/765def07-2de5-4fee-9fbe-2730d3d4567d)

## We can see the first password sent happened on 2020-05-26 14:51:19.

## If we keep looking through the Info column, we can see a Response from the server, 230 Login Successful stating when the attacker was able to break into our FTP server.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/c67d7a15-ac13-48ed-9105-2de03e0d684b)

## If we right-click and follow the TCP stream we can see what username and password the attacker used to access our server.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/2602920d-23e4-4cfc-bc74-d9c0fabb463f)

## Scrolling down in the TCP stream window you can see the RETR command, an abbreviation for Retrieve, which tells the server to send the requested file to the client. We can see that the user Chris logged in to the server and downloaded password.backup.

![image](https://github.com/CristianFernandez123/Analyzing-PCAPs-Lab/assets/161634608/33ef179a-151e-4a72-9607-9d2fb0d62dd0)

## Clicking on the line that references ‘RETR’ will take us to that packet in the PCAP. We can now close the Stream window and grab the Time value for reporting.
## Now that we have a full scope of what went on we can finish a full report on the incident.












