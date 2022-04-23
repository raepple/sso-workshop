# Single Sign-On between Microsoft Power Platform and SAP - Hands-On Lab Instructions

This repository contains the instructions (this file) and supporting material for the hands-on lab based on the blog post [Principal Propagation between Microsoft Power Platform and SAP](https://blogs.sap.com/2021/04/13/principal-propagation-in-a-multi-cloud-solution-between-microsoft-azure-and-sap-business-technology-platform-btp-part-iv-sso-with-a-power-virtual-agent-chatbot-and-on-premises-data-gateway/).

## Preparation

Make sure you meet the following prerequisites for the best workshop experience:

* Install SAP GUI on your local workstation and configure the connection to the SAP backend system (IP `20.105.160.186`, SID `A4H`) used in the workshop with the following parameters:

![](images/saplogonconfig.png)

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

The instructor will provide the required password credentials for the users in the table above.
## Business scenario

In this workshop you will implement a complete business scenario by integrating Microsoft and SAP technologies. From the end-user's perspective, you will use a chatbot in Microsoft Teams that lets you search and order office equipment in a product catalogue managed in a corporate SAP backend system. Access to the product data in the backend is restricted to certain product categories on a user level. For example, Jane Smith may only be allowed to order batteries and desk lamps, and John Doe can only order chairs.
![](images/usecase.png)
The main requirement from a functional and security perspective is to provide seamless and secure access between the chatbot and the backend system. The user must not be asked to provide any credentials when accessing the SAP backend to search the catalogue. Instead, the already logged-on user in the chatbot is securely propagated to the backend system. This requires a two-step security token exchange:
1. The token issed to the bot by Azure AD must be exchanged to a SAML assertion. This exchange is handled by Azure AD
2. The bot uses the SAML assertion to seamlessly log on the user in the SAP backend system and request an OAuth access token. This access token issed by the SAP backend system for the user can be used to call the OData service for the search operation in the backend.

![](images/overview.png) 

Please refer to the corresponding [blog post](https://blogs.sap.com/2021/04/13/principal-propagation-in-a-multi-cloud-solution-between-microsoft-azure-and-sap-business-technology-platform-btp-part-iv-sso-with-a-power-virtual-agent-chatbot-and-on-premises-data-gateway/) of this lab for a detailled explanation of the technical flow in this scenario.
## Exercises

You will implement the exercises of the hands-on lab in an isolated team environment to complete the implementation of the business scenario. Due to time constraints, the environment is pre-configured. The following exercises focus on the most relevant configuration steps to gain a deeper understanding of the different components and their integration.

Make sure you have a team number (1-10) assigned before starting with the exercises. Ask your instructor for help if needed.
### Exercise 1: Configure trust between Azure AD and the SAP backend system

Start the implementation by setting up the required trust relationship between the business user's Azure AD tenant and the SAP backend system. Without this trust relationship, the SAP system would reject the SAML assertion issued by Azure AD and seamless single sign-on fails in the scenario.

| Step | Description | Screenshot | 
| ---- | ----------- | ---------- |
| 1 | Open a browser and login to the SAML2 configuration of the [SAP backend system](https://vhcala4hcs.bestrun.corp:50001/sap/bc/webdynpro/sap/saml2) with user DEVELOPER. Use the client assigend to your team (001...010). **Note**: If have *not* maintained your local name resolution as described above, use this [URL](https://20.105.160.185:50001/sap/bc/webdynpro/sap/saml2) and confirm the security warning in your browser. | ![](images/0001.png) |
| 2 | Select the Trusted Providers tab. | ![](images/0002.png)   |
| 3 | Select **OAuth 2.0 Identity Providers** from the drop-down list. | ![](images/0003.png)   |
| 4 | Choose Add -> Upload Metadata File. | ![](images/0004.png) |
| 5 | Open a new tab in the browser and login as user teamX@bestruncorp.onmicrosoft.com to the [Azure AD admin center](https://aad.portal.azure.com). Select **Enterprise Applications** from the navigation menu. | ![](images/0005.png)  |   
| 6 | Select the application **ABAP 1909 A4H CAL 00n** (with **n** replaced by your team number) from the list. | ![](images/0006.png)   |
| 7 | Select **Single sign-on** from the navigation menu and click on the **Download** link next to the label **Federation Metadata XML**. Store the metadata file on your local workstation. Repeat the step and download the **Certificate (Raw)** file as well. | ![](images/0007.png)   |
| 8 | Go back to the previous browser tab and upload the Azure AD federation metadata file (`ABAP 1909 A4H CAL 00n.xml`). Click **Next**. | ![](images/0008.png)     |
| 9 | Select the option **Upload from file** and upload the raw certificate file (`ABAP 1909 A4H CAL 00n.cer`). Click **Next**.| ![](images/0009.png) |
| 10 | Click **Next**. | ![](images/0010.png)   |
| 11 | Click **Finish**. | ![](images/0011.png)   |
| 12 | Click **Edit**. | ![](images/0012.png)  |
| 13 | Under *Supported NameID Formats* click **Add**. | ![](images/0013.png) |
| 14 | Select *E-mail* from the list. Click **Ok**.| ![](images/0014.png)   |
| 15 | Click **Save** | ![](images/0015.png)   |
| 16 | Click **Enable** and confirm with **Ok**.| ![](images/0016.png)   |

You have now successfully established the trust relationship. For exchanging the SAML assertion into an OAuth access token, the SAP backend system also requires OAuth client credentials (a client id and secret) to authorize the calling system (i.e. the chatbot) to act on behalf of the user. OAuth client credentials are bound to a system user in the SAP backend which will be created next. 

### Exercise 2: Setup the OAuth Client for the Chatbot in the SAP backend system

| Step | Description | Screenshot | 
| ---- | ----------- | ---------- |
| 17 | Open a new tab in the browser. Login with your client (000 ... 010) and user DEVELOPER to the [OAuth2 configuration](https://vhcala4hcs.bestrun.corp:50001/sap/bc/webdynpro/sap/oauth2_config) in the backend system. Click **Create**. **Note**: If have not maintained your local name resolution as described above, use this [URL](https://20.105.160.185:50001/sap/bc/webdynpro/sap/oauth2_config)) and confirm the security warning in your browser.| ![](images/0017.png) |
| 18 | The system user **CHATBOT** is alread created in the backend system. Select it for the field **OAuth Client ID**. Provide a brief description (e.g. *Chatbot OAuth client*). Click **Next**.| ![](images/0018.png) |
| 19 | Deselect the option **SSL Client Certificate** in the **Client Authentication** step and click **Next**. | ![](images/0019.png) |
| 20 | Select the identity provider created in the previous exercise from the list. Click **Next**. | ![](images/0020.png)   |
| 21 | The SAP backend requires the same scope(s) assigned to the OAuth client as requested by the user. Therefore select the OAuth scope `ZPRODUCTSVIEW_CDS_0001` from value help list. This scope has been created with the deployment of the [Core Data Services (CDS) view](https://github.com/raepple/azure-scp-principal-propagation/blob/part3/SAPGateway/zproductsview.asddls) of the product catalogue search OData service. Click **Next**| ![](images/0021.png)   |
| 22 | On the summary page, click **Finish** | ![](images/0022.png) |

The backend system is now prepared to support the token exchange from the SAML assertion to an OAuth access token using the *SAML 2.0 Bearer Grant Type*.
### Exercise 3: Create the connection in Power Platform for the token exchange flow

The chatbot delegates the token exchange with the SAP system to a flow in [Power Automate](https://powerautomate.microsoft.com/en-us/). You will inspect the chatbot content and conversation logic in the Power Virtual Agents design tool and jump from there to the token exchange flow in Power Automate to configure the connection via the [On-Premise Data Gateway](https://docs.microsoft.com/en-us/data-integration/gateway/service-gateway-onprem).

| Step | Description | Screenshot | 
| ---- | ----------- | ---------- |
| 13 | Lorem ipsum |  |
| 13 | Lorem ipsum |  |
| 13 | Lorem ipsum |  |
| 13 | Lorem ipsum |  |

### Exercise 4: Test the chatbot in Power Virtual Agent web client

| Step | Description | Screenshot | 
| ---- | ----------- | ---------- |
| 0000 | Lorem ipsum |            |

### Exercise 5: Inspect the SAML assertion sent to the SAP backend’s OAuth server for the token exchange

| Step | Description | Screenshot | 
| ---- | ----------- | ---------- |
| 0000 | Lorem ipsum |            |

### Exercise 6: Change the user's authorizations in the backend and test again

| Step | Description | Screenshot | 
| ---- | ----------- | ---------- |
| 0000 | Lorem ipsum |            |

