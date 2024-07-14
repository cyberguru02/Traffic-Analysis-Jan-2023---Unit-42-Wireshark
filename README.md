# Traffic-Analysis: Jan-2023-Unit-42-Wireshark

I will be writing an in-depth guide on one of the traffic analysis exercises from “Malware-Traffic-Analysis.Net”, a resource website that provides simulations of PCAP files and malware infections for study.

​
![image](https://github.com/user-attachments/assets/af725c7e-29d7-49b8-9925-63295a91665d)

​

This guide will be on the exercise link: 2023-01 -- Unit 42 Wireshark Quiz, January 2023. Once you’ve opened the link, you are introduced to a 3rd-party site, palo-alto website, that will give you the rundown on the exercise.

​​
​
![image](https://github.com/user-attachments/assets/397ebb21-09a2-4fb6-8ccf-afb8c72ed045)

​

After reading through the rundown, we are told that a recent security incident occurred in association with the ‘Agent Tesla’ malware which may be possibly linked to OriginLogger. The rundown also provides a diagram showcasing the malware infection process by dropping an ISO image, executing its contents, generating HTTP and SMTP traffic masking the payload and exfiltrate data over SMTP. By grasping the scenario's context, we can set clear expectations on what to investigate, allowing us to proceed with our analysis.

 

Here are the questions for the exercise that we’ll answer as we investigate through the PCAP file:

 

When did the malicious traffic start in UTC?

What is the victim’s IP address?

What is the victim’s MAC address?

What is the victim’s Windows host name?

What is the victim’s Windows user account name?

How much RAM does the victim’s host have?

What type of CPU is used by the victim’s host?

What is the public IP address of the victim’s host?

What type of account login data was stolen by the malware?

​​​​​

                                     Here is the overview of the beginning of the PCAP. ​

​
![image](https://github.com/user-attachments/assets/f3f92fa3-676d-42e1-a253-173097302b7e)

​

Sifting through the PCAP we find an HTTP Packet,8, that is requesting a png file, Ztvfo.png, confirming that it’s our infected host, from the ip source number 192.168.1.27​

​
​
![image](https://github.com/user-attachments/assets/75139f51-c6e2-49bd-af18-2efc4b1eac01)

​
​

**What is the victim’s IP address? 192.168.1.27**

​

We can also simply find the linked MAC address with the infected host, bc:ea:fa:22:74:fb, at the beginning of the packet traffic during the initial ARP protocol.

​​​
![image](https://github.com/user-attachments/assets/137ebf69-f956-43b5-a489-82ade11373d6)

​

**​What is the victim’s MAC address? bc:ea:fa:22:74:fb**

​

To find the UTC the malicious traffic started, we can look at the request from the infected host and follow the conversation of the infection:


​
![image](https://github.com/user-attachments/assets/cc1fddcf-799a-4ea6-8abc-4443034f1da7)


​

​If we go back to the encoded payload request and follow the TCP stream, we can see the contents of the headers indicating that the UTC time is, 05 Jan 2023 22:51:00.

​

​![image](https://github.com/user-attachments/assets/2b6b98f0-65e1-4699-8ef6-4fa94b6883a0)


​

Note: Greenwich Mean Time and Coordinated Universal Time (UTC) have no time difference.

 

**When did the malicious traffic start in UTC? 05 Jan 2023 22:51:00**

​

To find the windows host name, I can simply filter for any packet that contains the “Windows” within the packet to search for the host device name:

​

​![image](https://github.com/user-attachments/assets/35762140-d359-456c-be7c-2316ccc93ff2)



​However, if there’s a case that you believe that the host name will not contain the usual free-text “Windows” in the host device name, you can simply filter for either nbns, SMB, or SMB2 traffic. We choose these types of traffic since they typically reveal the names of devices on a network which is required to help search for​ devices on a network or help share files between devices.

​
![image](https://github.com/user-attachments/assets/b92b465b-782e-4f4a-abff-564f59d7487e)
​

​It reveals that from the victim IP address it’s communicating from the browser server name, DESKTOP-WIN11PC, a common standard naming convention for Window devices, which is revealed as the victim’s host name.

​

**What is the victim’s Windows host name? DESKTOP-WIN11PC**


​The next step is to look up the victim's Windows user account, which can also provide the specific system details of what device the account is on. In Windows, our priority is to gather this information because malware often sends such data, including via SMTP (Simple Mail Transfer Protocol) traffic, as observed with Origin Logger. Therefore, our analysis should focus on identifying and examining SMTP traffic to uncover any exfiltrated information. 

​![image](https://github.com/user-attachments/assets/a0402abd-a15d-4ef1-bf90-0e147f864ea1)

​

Once following the TCP stream, we can see many detailed info about the user:


​![image](https://github.com/user-attachments/assets/f6713b56-2cd3-4de8-9670-5b8bf8270787)
​
 

We can see the victim device name, DESKTOP-WIN11PC, being communicated with and a generated email conversation from marketing@transgear.in to zaritkt@arhitektondizajn.com. Inside the contents of the email, it is filled with exfiltrated information about the victim which we can sift through to answer the following questions:

​

**What is the victim’s Windows user account name? windows11user**

**How much RAM does the victim’s host have? 32165.83 mb = 32GB**

**What type of CPU is used by the victim’s host? Intel(R) Core(TM) i5-13600K CPU @ 5.10GHz**

**What is the public IP address of the victim’s host? 173.66.46.112**

**What type of account login data was stolen by the malware? Login Credentials from Email client and Browsers such as ‘Thunderbird’ and ‘Edge Chromium ‘**

​

​![image](https://github.com/user-attachments/assets/c6352871-f8e1-42c7-ac09-98eab6fb9662)


​

In summary, this transcript illustrates how an attacker can use SMTP to exfiltrate sensitive information, such as system details and credentials, from a compromised system to a remote server controlled by the attacker. Such activities highlight the importance of monitoring SMTP traffic for detecting and mitigating data breaches and unauthorized access incidents.

​

**Takeaways:**

Working with Wireshark has been eye-opening. Beyond technical knowledge of network traffic analysis, it's given me practical insights into how data flows across networks. The practical work in troubleshooting using protocols such as TCP and UDP has really helped me grasp the concepts better. Using Wireshark has sharpened my skills in problem solving, whether it's diagnosing performance problems or identifying security risks, like malware or unauthorized access attempts. Wireshark isn't just a tool; it's a pathway to continuous learning in network administration and cybersecurity, where every session brings new discoveries and challenges.
