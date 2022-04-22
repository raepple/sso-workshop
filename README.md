# Single Sign-On between Microsoft Power Platform and SAP - Workshop instructions

This repository contains the workshop instructions (this file) and supporting material for the workshop based on the blog post [Principal Propagation between Microsoft Power Platform and SAP](https://blogs.sap.com/2021/04/13/principal-propagation-in-a-multi-cloud-solution-between-microsoft-azure-and-sap-business-technology-platform-btp-part-iv-sso-with-a-power-virtual-agent-chatbot-and-on-premises-data-gateway/).

## Preparation

Make sure you meet the following prerequisites for the best workshop experience:

* Install SAP GUI on your local workstation and configure the connection to the SAP backend system (IP `20.105.160.186`, SID `A4H`) used in the workshop with the following parameters:

![](images\saplogonconfig.png)

* *Optional*: Configure name resolution of the SAP backend's FQDN in your local hosts file by adding the following line: 
```
20.105.160.185 vhcala4hcs.bestrun.corp vhcala4hci.dummy.nodomain
```
  
* *Optional*: To avoid security warning in the web browser, add the backend system's TLS issuer (CA) [certificate](files/trustedca.crt) to the trusted root authorities on your local workstation. For Windows, run `mmc.exe`, add the Certificate Snap-in, and import the certificate to the `Trusted Root Certificate Authorities` folder:

![](images/mmc.png)  

## System access
You will group in separate teams (1 to 10) that use the following user accounts for administrative access to the SAP backend system, Microsoft Power Platform and Microsoft Azure subscription:
| Team | SAP Client | SAP User  | Power Platform |
| ---- | ---------- | --------- | ------------------- |
| 1    | 001        | DEVELOPER | team1@bestruncorp.onmicrosoft.com |
| 2    | 002        | DEVELOPER | team2@bestruncorp.onmicrosoft.com |
| 3    | 003        | DEVELOPER | team3@bestruncorp.onmicrosoft.com |
| 4    | 004        | DEVELOPER | team4@bestruncorp.onmicrosoft.com |
| 5    | 005        | DEVELOPER | team5@bestruncorp.onmicrosoft.com
| 6    | 006        | DEVELOPER | team6@bestruncorp.onmicrosoft.com |
| 7    | 007        | DEVELOPER | team7@bestruncorp.onmicrosoft.com |
| 8    | 008        | DEVELOPER | team8@bestruncorp.onmicrosoft.com |
| 9    | 008        | DEVELOPER | team9@bestruncorp.onmicrosoft.com |
| 10    | 010        | DEVELOPER | team10@bestruncorp.onmicrosoft.com |

The workshop instructor will provide you the required password credentials for the users in the table above.
## Workshop tasks
### Business scenario and requirements
In this workshop you will implement a complete business scenario by integrating Microsoft and SAP technologies. From the end-user's perspective, you will use a chatbot in Microsoft Teams that lets you search and order office equipment in a product catalogue stored on a corporate SAP backend system. Access to the product data in the backend is restricted on a user level and can be limited to certain product categories (e.g. Desk Lamps, Batteries, etc.). Please refer to the corresponding [blog post](https://blogs.sap.com/2021/04/13/principal-propagation-in-a-multi-cloud-solution-between-microsoft-azure-and-sap-business-technology-platform-btp-part-iv-sso-with-a-power-virtual-agent-chatbot-and-on-premises-data-gateway/) of this workshop for a detailled explanation of the workshop business scenario.

![](images/overview.png)  


