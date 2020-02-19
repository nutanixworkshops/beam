.. title:: Xi Beam - Cost Governance

.. Xi Beam - Cost Governance:

------------------------
Xi Beam - Cost Governance for Nutanix Private Cloud
------------------------

Overview
+++++++++

Xi Beam is a cost governance and security compliance service that works with both public clouds and Nutanix private cloud. This lab introduces Beam’s cost governance capabilities specifically for Nutanix.

What is Cost Governance? It is a set of rules by which you measure your cloud consumption and implement cost control policies. Specifically for the Nutanix Private Cloud, Beam provides the following cost governance capabilities: 

	- Granular Cloud Metering: know how much it really costs to run VMs in your Nutanix Private Cloud
	- Cost Center Reporting: tag-based automated reports to know how much a cost center is spending
	- Chargeback & Budgeting: drive accountability by allocating any untagged costs to appropriate cost centers and implement budget alerts 

Objective
++++++++++

This lab is intended to mimic a customer use case of identifying the true cost of running VDI on Nutanix Private Cloud across various users. By the end of the lab, you will learn: 

	- How to use a TCO model to calculate the cost of Nutanix clusters and VMs 
	- How to use Prism categories to allocate those costs to a cost center for VDI spend reporting 
	- How to implement Chargeback to ensure costs are allocated to appropriate cost centers 
	- How to set up alerts to be notified before costs exceed a certain budget

	.. figure:: images/beam_vm_costing.png

Pre-Requesites
++++++++++++++++

Beam is a SaaS service and does not require any installation for the cost governance lab. However, there are some prerequisites for this lab: 

#. Beam SaaS login credentials:
	- Navigate to https://beam.nutanix.com/
	- Select “*Sign in with My Nutanix*” and then “*Login with your Company ID*”
	- Enter Company ID: *beam-lab@nutanix.com*
	- Enter Unique ID: *nutanix6-ad*
	- This will take you to an Active Directory login page where you will enter a username and password. Use your login credentials assigned from the cluster assignment spreadsheet.
#. Using an appropriate Nutanix cluster. The cluster needs to have some product licenses associated with it in the Nutanix Salesforce database. This has already been done for the purpose of the lab.
#. Enable Prism Pulse so that Beam can pull details of VMs running in that cluster. This has already been done for the purpose of the lab.

If this is your first time accessing the Beam lab, you may see two popup messages explaining how Beam calculates the cost data for Nutanix products. You may read the messages and proceed to the lab environment.

	.. figure:: images/beam_02.png
	
	.. note::
	
	  This lab uses the same active directory setup as the HPOC clusters. Use the login credentials assigned to you from the GTS cluster assignment spreadsheet

Private Cloud Cost Metering
+++++++++++++++++++++++++++

Nutanix account configuration
..............................

In order to start using Beam, you will need to configure a customer’s Nutanix account with Beam. This just requires entering a license key associated with any customer purchase. 

Login to the Beam SaaS instance using the credentials provided in the above section. Navigate to **Configure -> Nutanix Account**. When prompted, enter a license key. This has already been done for the purpose of this lab.

	.. figure:: images/beam_03.png
	
	.. figure:: images/beam_04.png
	
Product Costs
..............

The next step is to ensure Beam has accurate license cost data for all Nutanix products. Navigate to Configure -> Nutanix Cost Configuration. Once you’ve entered the license key, Beam pulls in the entire purchase history from the customer’s Salesforce account. 

Beam automatically populates product costs based on assumed market price for all Nutanix products - hardware and software. The actual cost paid by customers may vary slightly depending on the partner discount which Beam does not have visibility into. However, you can easily configure these product costs to match what was actually paid.

	.. figure:: images/beam_05.png
	
TCO Configuration
.................

Beam uses a built-in **total cost of ownership (TCO) model** for Nutanix that provides out-of-the-box visibility into the true cost of running the Nutanix private cloud. These costs are eventually used to calculate granular VM level costs. It is important to configure the TCO model accurately so that you have more accurate VM costs.

The TCO model includes several cost heads that are automatically populated depending on the number of Nutanix nodes and some industry standards that are built into the model. The out-of-the-box TCO calculations are accurate to a good degree. They can be further fine-tuned to meet customer need. Learn how to configure the TCO model: 

#. Navigate to **Configure -> Nutanix Cost Configuration**, switch to the Cluster tab, and search for cluster ID ending in 4d3b. Click on **Edit TCO** next to that cluster.

	.. figure:: images/beam_06.png
	
If you do not see the **“Edit TCO”** option, click on **“Define Cluster Cost Allocation”** and ensure that Total Cost of Ownership is selected as the cost model.

#. Navigate through the various cost heads to familiarize yourself with them

	- **Hardware**: Includes all Nutanix appliance costs which are automatically pulled from customer’s purchase history in salesforce. These costs are amortized on a monthly basis assuming a hardware life of 60months. 
	Any third-party hardware costs are added automatically as well. Average cost per third-party appliance is assumed to be $12,000. Memory costs for the whole cluster can be added manually if you know the monthly cost per GB of RAM.

	.. figure:: images/beam_07.png

	- **Software**: Includes all Nutanix software license costs which are automatically pulled from customer’s purchase history in salesforce. Costs associated with any Nutanix nodes running vSphere are also automatically calculated assuming a vSphere license cost of $210/processor and 2 processors per node. 
	Any additional software costs, such as a third-party application performance monitoring software cost can be added manually. All software costs are amortized on a monthly basis.
	
	.. figure:: images/beam_08.png

	.. note::
	
	  The Nutanix hardware and software license costs are based on assumed market prices and may not reflect the actual price paid by customers because Beam does not have visibility into partner provided discounts.

	- **Facilities**: Includes power and cooling costs, and datacenter space costs for the datacenter used to run the Nutanix Private Cloud. 
	The power and cooling costs are automatically calculated depending only on the number of Nutanix nodes. Amount of power consumed per node and a power usage effectiveness (PUE) ratio are built into the model. The TCO model assumes cost of power to be $0.1/kWh based on US standard. This cost can be configured depending on customer geography. Monthly power and cooling costs = Number of nodes * power consumed per node in kWh * cost per kWh * (number of hours in a month)*PUE.
	
	The datacenter space costs are also automatically calculated depending only on the number of Nutanix nodes. It is assumed that each node needs 2 rack units, there are 42 rack units per rack, and the average cost per rack is $1,400/month. Monthly datacenter space cost = {(Number of nodes * 2 rack units)/42 rack units per rack}*($1,400)

	.. figure:: images/beam_09.png
	
	.. figure:: images/beam_10.png

	- **Telecom**: Includes Ethernet/top-of-rack switch costs that are automatically calculated depending only on number of nodes. The TCO model assumes each node uses 2 ports on a switch, there are 48 ports per TOR switch, and the cost of each TOR switch is $1,250 amortized on a per-month basis. Monthly telecom costs = {(Number of nodes*2 ports)/48 ports per switch}*($1,250)
	
	.. figure:: images/beam_11.png
	
	- **Services**: This cost head includes any Nutanix or third-party services that you may have paid for. These costs are entered manually by the user and amortized to a monthly basis by TCO model.
	
	.. figure:: images/beam_12.png
	
	- **People**: This cost head includes the cost incurred on IT Admin salaries for the administrative staff employed to maintain your Nutanix Private Cloud. The TCO model assumes a salary of $150,000 for internal admins and $80,000 for external admins based on US standards. You can specify what % of your Nutanix nodes are outsourced to external admins and what is maintained by internal admins. Salary amounts can be configured too.
	
	.. figure:: images/beam_13.png

The power of the TCO model lies in being able to centralize all costs associated with a private cloud into one unified view and providing a good approximation of a customer's private cloud costs without any configuration. At the same time, the TCO model is highly customizable and can also be configured separately for each cluster.


Cluster and VM Costing
......................

The next step is to allocate the cluster level costs to individual VMs running on the cluster. It is mandatory to have Prism Pulse enabled so that Beam has the data on VM state and resources allocated to each VM on that cluster.


The total cluster level costs (calculated using the TCO model) are allocated to each VM depending on the number of hours that the VM is up and running and the capacity allocated to that VM relative to the overall capacity on the cluster. The CBL model is used to calculate cost per vCPU, cost per GB of storage and cost per GB of RAM. Those per unit costs are multiplied by the number of vCPUs, storage and memory allocated to each VM to get total VM costs. These costs are calculated out-of-the-box without needing any customer configuration.

#. Close the TCO view, click **Go Back** and navigate to **Analyze -> VM Costing** tab. Search for cluster ID ending in *4d3b* and go to **View Details** to see the detailed TCO based cost breakdown for this cluster.

	.. figure:: images/beam_14.png

#. Note that the cost of each individual VM running in this cluster has been automatically calculated. If the TCO model has been accurately configured, these costs represent the true cost of running that VM in the Nutanix Private Cloud.
	
	.. figure:: images/beam_15.png
	
This concludes the walkthrough of Beam’s cost metering capabilities for Nutanix Private Cloud. The next step in implementing Cost Governance is to create cost center views.

	.. Note::
	
	  It takes a few hours for VM costing data to show up after a Nutanix account is configured in Beam. The TCO model is baked into the product and VM costs will be calculated out-of-the-box using the default values of the TCO model. The model can be fine-tuned depending on customer need.

Cost Center Reporting
++++++++++++++++++++++

Creating a Cost Center
......................

Now that we know what individual VMs cost to run on Nutanix, we can create cost views that aggregate consumption across various VMs and clusters. This is done using Prism Categories as tags. Depending upon how Prism Categories are defined, these cost centers can help to track spending across various users, teams, applications, geographies, etc.

#. Navigate to the **Global** Organization view from the main menu and go to the **Chargeback** tab. You may notice some cost centers previously created by other users.

	.. figure:: images/beam_16.png

#. Select **Create** then **Cost Center**. Provide a name for the cost center and click on **Define Cost Center**.	
	
	.. Note::
	
	  It takes up to 24hrs for VM costing data to show up after a Nutanix account is configured in Beam. The TCO model is baked into the product and VM costs will be calculated out-of-the-box using the default values of the TCO model. The model can be fine-tuned depending on customer need.

	.. figure:: images/beam_17.png
	
	.. figure:: images/beam_18.png
	
#. Define the Cost Center as following: 
	- **Cloud** -> *Nutanix*
	- **Parent Account** -> *Nutanix Cost Demo Account*
	- **Sub accounts** -> *Search for the cluster ID ending in 4d3b*
	- **Tag Pair: Key** -> *nx:App, Value -> VDIXXX* 	
	
The *XXX* will be a three-digit number. You may select any number between 001 to 080. This is being done to provide a unique key-value pair for each lab attendee because each key-value pair can only be in a unique cost center to avoid double counting of VM costs in different cost centers.
	
	.. figure:: images/beam_19.png
	
Select **Save Filter** to save the key-value pair used as a filter. You can add multiple key-value pairs to a cost center definition. Select **Save Definition** to save the definition of the cost center, and **Save Cost Center** to exit the view and go back to the Chargeback screen.

You have now created a cost center which will aggregate costs from all Nutanix VMs carrying the tag key *App* and tag value *VDIXXX*. This cost center can now be used to report the cost of VDI running on Nutanix infrastructure by adding the cost of VMs carrying the tag values you specified. You may add further Prism Categories as filters to the cost center definition. For example you could add a **Region** Category as tag key and **Europe** or **Asia** as tag values as long as those are defined in Prism. This would allow you to create Cost Centers to track VDI spending across different regions.

	.. Note::
	
	  Each Key-Value pair can only be added to a unique Cost Center. If you get an error message when you define your Key-Value pair, it is likely because another user already added that Key-Value pair to their Cost Center. Please select a different Key-Value pair. Also, It takes up to 48hrs for new Prism Categories to show up in Beam. 
	
	.. figure:: images/beam_20.png
	If you see this error message, just select a different value for the tag key-value pair.
	
Optional - the cost center definition can be made to be truly multi-cloud. If your customer wants to extend their cost center definition to also include public cloud resource costs, that can be done in the same way by adding public cloud accounts and tag-key pairs to the same cost center definition. This is a very powerful capability of Beam immensely helping customers that use both public and private clouds by providing a unified view of all cloud resource costs in the same cost center.

	.. figure:: images/beam_21.png
	
Some customers may want to have several cost centers reporting to a common parent entity. For example, you may want to track the costs separately for different dev and prod teams all reporting to the same Engineering department. You can do this in Beam by defining a Business Unit which is nothing but a combination of multiple cost centers. Each Cost Center can only belong to one Business Unit.

	.. figure:: images/beam_22.png

Chargeback & Budgeting
++++++++++++++++++++++

Chargeback Unallocated Spend
............................

Not all VMs may be tagged with Category values that you specify in cost centers. Often times you will find that there will be spending that did not fit a cost center definition but does need to be accounted for. This can be done through *Chargeback*.

#. Navigate to the **Chargeback -> Unallocated** spend view. Search for the cluster ID ending in *4d3b*. Click on **View Details** to see the details of spend on this cluster that did not get allocated to any cost center.

	.. figure:: images/beam_23.png
	
#. If you find any unallocated spend from some VMs, you can select **Allocate** and choose the cost center(s) that you want to allocate that spend to. You can also split the spend across multiple cost centers. Select the cost center you had created, **XY-BeamLab**, and allocate 100% of the spend of this VM to that cost center. You only need to do this once. Any future spending by the same VM will be automatically allocated to that cost center.

	.. figure:: images/beam_24.png

This completes the Chargeback portion of the lab. This feature is extremely helpful to identify shadow spending outside of a customer’s cost center and business unit structure, and allows a financial admin to more accurately map cloud consumption to appropriate owners so that customers can be aware of who is responsible for spending in their cloud.

Budget Alerts
.............

Define a budget for a cost center and set up a budget alert.

#. Navigate to the **Budget** tab and click on **Create a Budget**. Select the budget type to be **Business Unit/Cost Center** based Budget. Alternatively, Beam also allows you to create a custom resource group using a combination of accounts, services, and tags, and then set up budget alerts on the custom resource group. For this lab, we will use the cost center that you created in the previous section: *XY-BeamLab*

	.. figure:: images/beam_25.png
	
	.. figure:: images/beam_26.png

#. Use the **Manual Allocation** option when defining a budget. This will allow you to enter some numbers for the budget at an yearly, quarterly or monthly level. Enter the annual budget to be $100,000. It will be allocated equally to each month.

	.. figure:: images/beam_27.png
	
#. In the final step, create a budget alert at a quarterly level. Set the threshold to be 85%. Make sure your email address is in the alert notifications field. Click **Save** when done.

	.. figure:: images/beam_28.png
	
	.. figure:: images/beam_29.png

You have now created a budget alert to be notified when spending in your cost center goes above a certain threshold. 

This completes the Private Cloud Cost Governance lab. You may log out of your Beam account

Takeaways
+++++++++

- Beam’s cost governance module helps you identify cost of VMs running on Nutanix, allocate them to cost centers, setup chargeback reports & budget alerts.
- You can create multi-cloud cost centers using public cloud tags and Prism categories to track spending across both private and public clouds
- Nutanix costs can be configured using a highly customizable TCO model that helps you identify your true cost of running your private cloud
- Beam helps you keep your cloud spending in control and drives financial governance in a multi-cloud environment
