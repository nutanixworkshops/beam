.. title:: Xi Beam - Security Compliance

.. Xi Beam - Security Compliance:

--------------------------------------------
On-Premises Security Compliance with Xi Beam
--------------------------------------------

Overview
+++++++++

Xi Beam is a security compliance and cost governance service that works with both public clouds and Nutanix private cloud. This lab introduces Beam’s security compliance capabilities specifically for Nutanix. The same capabilities also exist for public clouds.

What is Security Compliance? It is the practice of establishing and validating compliance with a security baseline through automated auditing of cloud resource configurations and easy remediation of security vulnerabilities. Specifically for the Nutanix Private Cloud, Beam includes the following security compliance capabilities:

	- Security Auditing & Remediation - global security audit results and remediation steps to improve the security of Nutanix Private Cloud
	- Regulatory Policy Compliance - details of your organization’s compliance with regulatory policies like PCI-DSS and STIG
	- Custom Audits - very easily create your own custom audits to go beyond the out-of-the-box provided security audits

Objective
++++++++++

This lab is intended to mimic a Nutanix private cloud customer’s use case of identifying critical security vulnerabilities that may have been caused due to misconfigured cloud resources. By the end of the lab, you will learn:

	- How Beam reports on security misconfigurations in Nutanix private cloud
	- How to take remediation actions to improve private cloud security
	- How to create your own custom security audits
	- How to setup rules to be notified upon new security audit failures

	.. figure:: images/beam_sc_image1.png

Pre-Requesites
++++++++++++++++

Beam’s security compliance capabilities for Nutanix private cloud have two components - a Beam SaaS instance and a Beam-VM that is installed on a Nutanix cluster. Here are some prerequisites for this lab:

#. Beam SaaS login credentials:
	- Navigate to https://beam.nutanix.com/
	- Select “*Sign in with My Nutanix*" and then "*Login with your Company ID*”
	- Enter Company ID: *beam-lab@nutanix.com*
	- Enter Unique ID: *nutanix6-ad*
	- This will take you to an Active Directory login page where you will enter a username and password. Use your login credentials assigned from the cluster assignment spreadsheet.
#. Beam-VM: Beam requires a VM installation on a Nutanix cluster to report certain data about resource configurations from Prism APIs to the Beam SaaS engine. One Beam-VM is required per Prism Central install and it can be installed on any cluster managed by that Prism Central instance. This has already been done for the purpose of the lab.
#. Most of the security audits in Beam are dependent on AHV as the hypervisor.

If this is your first time accessing the Beam lab, you may be navigated to Beam’s cost governance module for Nutanix and may see two popups explaining how Beam calculates the cost data for Nutanix products. You can ignore these messages for the security compliance lab, close the pop ups and navigate to the security compliance module.

	.. figure:: images/beam_sc_image2.png

	.. figure:: images/beam_sc_image2b.png

	.. note::

	  To login to the Beam SaaS instance, this lab uses the same active directory setup as the HPOC clusters. Use the login credentials assigned to you from the GTS cluster assignment spreadsheet.

Architecture
+++++++++++++++++++++++++++

The Beam-VM needs to have bi-directional communication with Prism Central as well as Beam SaaS instance. Security auditing data is generated using data from v3 APIs. One VM needs to be installed per Prism Central instance. The Beam-SaaS instance is run in AWS, although that does not impact the user in any way. Communication between the Beam-VM and Beam-SaaS instance happens over a secure gRPC channel.

	.. figure:: images/beam_sc_image3.png

Beam-VM Installation
+++++++++++++++++++++++++++

A Beam-VM has already been installed on a lab cluster and configured with a Prism Central account using the following steps:

1. The Beam-VM image is available at the Xi Beam downloads page on portal.nutanix.com.

A VM has been created in Prism Central using the above image. You may log into this Prism Central account to view it:
URL: https://10.55.20.42:9440/
Username: *admin*
Password: *techX2020!*

The Beam-VM needs 2 vCPUs (at least 1 core per vCPU), 2GB RAM and 15GB storage. The VM name used for this lab in Prism Central is *BeamVM-DoNotDelete*.


	.. figure:: images/beam_sc_image2c.png


	.. figure:: images/beam_sc_image2d.png


2. If the VM is powered on, click Launch Console and use the following default credentials to log in:
username: *beam*
password: *b3@mMeUp!*

	.. note::

	  Port 9440 needs to be open for Beam-VM to connect to Prism Central and port 443 needs to be open for Beam-VM to connect to Beam SaaS.

3. Network settings have already been configured in the VM by providing an IPv4 address, a Netmask, Gateway and DNS server address.

4. A local instance of the Beam UI application should already be running in the VM. If not, go to https://beam.local/ in a browser. You can verify that Prism Central details have been configured in this Beam UI application and a token has been generated which is also configured in the Beam SaaS instance


	.. figure:: images/beam_sc_image2e.png


Click on "*Prism Central Connection*" and verify that the PC details have been configured.


	.. figure:: images/beam_sc_image2f.png


5. You do not need to generate a new token for this lab but you can familiarize yourself with where the token is generated in the Beam UI application and entered in the Beam SaaS instance.


	.. figure:: images/beam_sc_image2g.png


6. After logging into the Beam SaaS account using the credentials provided in the prerequisites section, go to **Configure* -> Nutanix Accounts** and validate that a token is entered there with the PC name *PC-RTP-POC020*. Note that this token may be different in Beam SaaS instance because of the lab cluster setup having been refreshed since this script was created. During an actual installation, you will also be able to select which clusters you want to configure in Beam. The HPOC cluster RTP-POC020 has been configured for this lab.


	.. figure:: images/beam_sc_image4.png

Security Auditing & Remediation
+++++++++++++++++++++++++++

Global Security Posture
.................

Beam provides a global dashboard of the security posture of your Nutanix environment. This dashboard is generated using the results of the security audits in beam. Security audits are categorized by severity level - high, medium or low severity - based on security best practices. Beam comes with more than 1,000 security audits out-of-the-box across public and private clouds with 500+ security audits for Nutanix private cloud.
The global security summary map serves to easily identify the number of security issues globally and their severity type.


	.. figure:: images/beam_sc_image5.png

This dashboard also provides a timeline of the total number of security audit failures. The timeline helps to easily identify if the overall security posture is improving over a period of time or getting worse. Scroll down the page to see the compliance timeline.


	.. figure:: images/beam_sc_image6.png

Click on “High Severity” to at the top of the dashboard to dig into the details of high severity audit failures identified by Beam.

	.. figure:: images/beam_sc_image7.png


Audit Report & Remediation
.................
You will be brought to the **Compliance Remediation -> Audit Details** tab. Here you can see the details of security audit results categorized by audit type:

	- Host Security
	- Infrastructure Security
	- Network Security
	- Data Security
	- VM Security
	- Access Security
	- Others

Let’s walk through some of the audit types to understand some examples of what Beam can audit for in the Nutanix environment. Click on **“Data Security”**.


	.. figure:: images/beam_sc_image8.png


Here you will see the audits categorized as Data Security type. You will see that Beam has identified some clusters where data-at-rest (DAR) encryption has not been enabled. This is a critical security vulnerability. Click on the audit name to view details.


	.. figure:: images/beam_sc_image9.png


Here you see details like Cluster UUID and Cluster Name so that you can easily identify the cluster details where DAR needs to be enabled. Let’s go back and look at more audit details. Go back two steps and click on **“Network Security”**.


	.. figure:: images/beam_sc_image10.png


Here you will see details of some of the network security audit types including VMs potentially open to all external traffic on certain ports. In this case they are TCP ports 2483 and 1521 but Beam can scan a huge range of TCP and UDP ports. Click on audit details for port 2483.


	.. figure:: images/beam_sc_image11.png


In the audit detail you can easily identify Cluster details, Host IP and VM name. Beam also provides remediation instructions so that users can take the necessary action to shut down global access on these ports. Click on **“how to fix”** to see these remediation details.


	.. figure:: images/beam_sc_image12.png


	.. figure:: images/beam_sc_image13.png


	.. note::

	  Beam runs all security audits and reports on audit failures approximately every 6 hours. This time period will be shortened in upcoming releases.

Let’s look at one more audit type. Go back two steps and click on **“Host Security”**. Here you will see a long list of STIG requirements that Beam audits for. Click on *STIG requirement RHEL-07-040400*.


	.. figure:: images/beam_sc_image14.png


You will see that this is an audit on checking what type of hash algorithms are employed by SSH daemon. If the SSH daemon is configured with a Message Authentication Code (MAC) that does not use the FIPS 140-2 hash algorithm then it will be identified by Beam.


	.. figure:: images/beam_sc_image15.png


Beam has hundreds of such audits for Nutanix environments. It takes some time to go through the whole list of audits so we will skip that for this lab. But you can find the whole list of audits by going to **Configure -> Compliance Policy** in the drop down menu in the top-right corner and viewing the *Beam Security Policy*.


	.. figure:: images/beam_sc_image16.png


	.. figure:: images/beam_sc_image17.png



Regulatory Policy Compliance
++++++++++

In addition to various security audits included in Beam’s default security policy, Beam also provides compliance reports with regulatory policies such as PCI-DSS with more policies like HIPAA, NIST, etc. coming soon. Go back and navigate to the **Compliance** tab. You will see an overall view of the level of compliance with PCI-DSS and also STIG policy (which comprises of all the STIG related audits that Beam performs.) Click on the PCI-DSS compliance policy to see the details.


	.. figure:: images/beam_sc_image18.png


PCI-DSS Compliance
.................

Beam provides an extensive list of all actions that an organization should take to comply with regulatory policies like PCI-DSS. The regulatory policy compliance view can be considered as **a system of records** to identify your compliance with all tasks that need to be performed to comply with regulatory policies.

These can be categorized into three types - Process, Documentation and Configuration related tasks. Process and Documentation tasks related to security processes and supporting documentation that you need to maintain. Configuration tasks relate to the automated resource configuration audits that Beam runs. Click on section 1.1 to see details.


	.. figure:: images/beam_sc_image19.png


Here you see extensive details of all steps needed to be taken to comply with PCI-DSS policy.
**Process checks:** One of the key requirements is having “a formal process for approving and testing all network connections”. Do you have such a process in place? If so, you can click on *Mark as Resolved* and upload proof of the process that your organization has in place.

**Documentation checks:** You need to have a “current network diagram of connections between cardholder data environment and other networks”. If you have this diagram, you can click on *Mark as Resolved* and upload that diagram as proof.

**Configuration checks:** Is your network actually configured in a way to have a “firewall between a DMZ and external internet”? If not, Beam would identify this using its automated audit checks. If a firewall was not in place, Beam would flag it as a security issue. Another example of a configuration task is “restricting inbound and outbound traffic”. This is an audit that Beam identified as having failed. Click on “5 issues detected: to see details.


	.. figure:: images/beam_sc_image20.png


We see the details of TCP ports allowing all external traffic and therefore the PCI-DSS requirement of “restricting inbound and outbound traffic” is not satisfied and your organization will not be in full compliance with PCI-DSS policy.


STIG Compliance
.................

Go back one step, click on *STIG policy* and familiarize yourself with the STIG compliance view.


	.. figure:: images/beam_sc_image21.png


Here we see details of all audits in the context of compliance with STIG policies - which ones passed and which ones failed.



Custom Security Audits
++++++++++

In addition to the 1000+ security audits across Nutanix and Public clouds, Beam also allows you to very easily create your own custom security audits. This greatly expands the products capabilities in terms of what it can be used to audit. Once a custom audit is created, it is added to the default Beam Security Policy and runs in an automated fashion with all of the other out-of-the-box audits.

Beam Query Language
.................


	.. figure:: images/beam_sc_image22.png


Navigate to **Configure -> Custom Audits** and click *Add New Custom Audit*, and select Nutanix.


	.. figure:: images/beam_sc_image23.png


You will see a Query Editor. This query editor has been built using a SQL based query language just called **Beam Query Language**. You will see a drop-down menu to help you start building a custom edit. We want to create an audit that checks for VMs with network security group rules allowing inbound traffic over public IP 0.0.0.0. Here are the steps to create this audit:

**From:** Select *NX*. You will see options for other clouds too.The next popup menu will give you a lot of resource options. Select *VM*


	.. figure:: images/beam_sc_image2h.png


The next variable will be **Where:**. Select *Category* and then *NetworkSecurityGroup*. This will show all auditable capabilities categorized for network security groups.


	.. figure:: images/beam_sc_image2i.png


Now we want to check for security group rules that govern how inbound traffic flows to VMs. Select **AppRule** and then *InboundAllowedGroup* to specifically check for the rules on inbound traffic flow.


	.. figure:: images/beam_sc_image2j.png


Lastly, we want to check when inbound traffic is allowed over a specific IP address, which is public IP 0.0.0.0. Select **IpSubnet** and then *ip*. You will see several mathematical functions. Select *contains* and placeholder text *foo* will show up. You can click on it and replace it with 0.0.0.0


	.. figure:: images/beam_sc_image2k.png


This completes the custom audit. You can select *Save Audit*.


	.. figure:: images/beam_sc_image2l.png


Specify a name for the audit, audit description, severity type and how you would like to categorize the audit. Please use your initials when saving the audit name, such as *XY-BeamLab*. This will help prevent multiple people choosing the same audit name.


	.. figure:: images/beam_sc_image2m.png


Deploy the audit and you are done! In just a few minutes we were able to create a highly customized security audit without needing to know any coding or doing any configurations!

	.. note::

	  The Beam Query Editor comes with a “Query Library” where you can see the custom audits created by others in your organization. You can also see “Entity Details” to know the details of what entities the query editor can support.


Alert Notification Rules
.................

The last step in this lab is to create a notification rule so that you will be sent an alert when a critical audit failure happens. This can be done either through daily system generated reports or custom notifications.

Go to **Configure -> Integration Rules** and click on *Create New Rule*


	.. figure:: images/beam_sc_image31.png


Here you can define the criteria for being alerted. This workflow can also be used to send notifications to Splunk or create Webhooks. Under the option of “Event Type” select *Any Issue State Change (All)*. This will ensure that the notification is valid for all state changes of a security issue including new issues, resolved issues and suppressed issues.


	.. figure:: images/beam_sc_image32.png


Delete AWS and Azure from the filter criteria. Click edit on filter criteria next to Nutanix. In the popup that shows up, ensure that the Cloud is *Nutanix*, Click edit next to “selected audits” and find the custom audit name you had created in the previous section. Click the blue check mark, save and close. This defines the alerting criteria.


	.. figure:: images/beam_sc_image33.png


Now you want to define what happens when the alert criteria is fulfilled. Select “New action” from the menu on the left, select “send email” and provide your email address. You may select the default email template. Validate the email address, save and close the notification rule.


	.. figure:: images/beam_sc_image34.png

	.. figure:: images/beam_sc_image35.png


Provide a name and description such as *XY-BeamLabRule*.


	.. figure:: images/beam_sc_image36.png


You have now defined a notification rule that will send an email notification whenever the definition of your custom audit fails. In this case, it will be when a CVM running in a cluster using AHV is about to run out of disk space. You can create any number of such custom audits.

This completes the Private Cloud Cost Governance lab. You may log out of your Beam account

Takeaways
+++++++++

- Beam’s security compliance capabilities can identify resource misconfigurations using 1000+ security audits across on-premises private clouds built on Nutanix and public cloud infrastructure.
- Beam also makes it very easy to create your own custom-audits and get alerted on audit failures that you care about.
- Nutanix costs can be configured using a highly customizable TCO model that helps you identify your true cost of running your private cloud
- You can also use Beam as a system-of-records to validate your compliance with regulatory policies like PCI-DSS.
