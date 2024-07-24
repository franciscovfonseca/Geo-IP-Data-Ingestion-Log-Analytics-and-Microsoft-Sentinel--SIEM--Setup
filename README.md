<br>

<h1 align="center">Log Analytics Workspace & Microsoft Sentinel (SIEM) Setup</h1>

<br>


![Banner](https://github.com/user-attachments/assets/1cc4caf7-8f6c-4747-aaeb-eeee80212ba0)
<br />
<br />

In this lab we'll set up **Log Analytics Workspace** as our **Central Log Repository****, as well as **Microsoft Sentinel** as our **SIEM**.

Basically we're going to create what we call a **"Watchlist"** inside of our **SIEM** (Microsoft Sentinel).

This Watchlist is going to essentially be a big long list of **Network Blocks**, with the corresponding *Longitude*, *Latitude*, *City Name* and *Country Name*.

And basically we'll use this to derive **Geo-location from Attacker's IP Addresses**.

So when somebody **Attacks our Network**, or for example they fail to login to our Virtual Machine, we'll be able to:
1. Inspect that Log
2. Match the Log to a given Network Block
3. and Plot it on the World Map

This way we'll be able to clearly see **Where the Attacks are Coming From**, according to their **Geo-Location**.

<br />

<details close> 
<summary> <h2> 1️⃣ Download the Geo-Data File</h2> </summary>
<br>

The first thing we're going to do is **[Download this Geo-Data CSV file](https://github.com/joshmadakor1/Cyber-Course-v2/blob/main/Sentinel-Maps(JSON)/geoip-summarized.csv 
)** onto our Desktop.

It will open up a new tab, and you can **Download the Raw File**:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>2️⃣ Generate some Failed RDP Logs against the windows-vm</h2> </summary>
<br>

> Then we're going to **Create a Log Analytics WorkSpace** ➜ our **Central Log Repository**
> 
> This will be our **Centralized Repository** for **Collecting, Storing & Analyzing Log Data** from various **Azure Resources and Services**.

<br>

We'll go back to the **Azure Portal** > search for **"Log Analytics WorkSpace"**:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

We'll click **"Create log analytics workspace"** and input the following details:
- Resource group: ```RG-Cyber-Lab```
- Name: ```LAW-Cyber-Lab-01``` ➜ ⚠️ it has to be globally unique
- Region: ```East US 2``` ➜ put it in the **Same Region** as the other resources from our environment

We can then just click **"Review + Create"**:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

✅ Our **Log Analytics Workspace** is now created.

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>3️⃣ Setup Microsoft Sentinel & Connect it to our Log Analytics Workspace</h2> </summary>
<br>

> So after creating our **Log Analytics Workspace**, we're going to attach our **Sentinel** instance to it.
> 
> And then ultimately we'll be able to **Query Logs** and **Plot them on a Map**.
<br>

Go back to the Azure Portal > search for **"Microsoft Sentinel"**:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

Click **"Create Microsoft Sentinel"**:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

This next step is when we Add the Log Analytics Workspace we just made to our Microsoft Sentinel instance:

- We'll click on our **Workspace** > and click **"Add"**:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

✅ We just made the connection between the **Microsoft Sentinel** and the **Log Analytics Workspace**.

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>3️⃣ Create the Geo IP Watchlist</h2> </summary>
<br>

> Once Sentinel is setup ➜ we'll go into Sentinel and we're going to create what's called a Watchlist.
> 
> The Watchlist is going to be comprised of that Geo Data from the file we downloaded earlier.
>
> Then later we'll use the Geo Data to Plot Attacker's IP Addresses on a Map.

<br>

First we'll through the Azure Portal to **Microsoft Sentinel** > and click on our Sentinel Instance connected to the LAW: ```LAW-Cyber-Lab-01```

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

Then we'll go to **"Watchlists"** on the left ➜ and we're going to **Create a New Watchlist**

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

Now for the Geo IP Watchlist we'll use this details:
- **General Tab**:
  - Name & Alias: ```geoip```

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

- **Source Tab**:
  - Source type: ```Local file```
  - File type: ```CSV file with a header (.csv)```
  - Number of lines before row with headings: ```0```
  - Upload file ➜ We're going to browse for the file that we downloaded to our Desktop earlier:
    - ```geoip-summarized.csv```

<br>

⚠️ You should be able to see a "valid-looking preview" of the Geo IP File on the right

<br>

- Still inside the **Source Tab**:
  - Search Key: ```network```

💡 The ***Search Key Column*** is what we're going to use to match the Attacker's IP Addresses to different Network Blocks.

<br>

Click **"Review + Create"** to **Create the ```geoip``` Watchlist**

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

This should take a while:

- Allow time for these files to “upload” from your computer into Sentinel / Log Analytics Workspace.

⚠️ There are about 27k rows/records












![azure portal](https://github.com/user-attachments/assets/55a2c962-8229-4fa1-89df-d7a9264e9e45)

Now inside the **attack-vm** > open **Remote Desktop Connection** > Paste the **Public IP Address of the Windows VM** and connect

![azure portal](https://github.com/user-attachments/assets/b1790beb-344f-4d2d-af49-9a6f30f282f8)

For the **Credentials** ➜ use some random made up **Username & Password** ➜ username ```josh``` for example

Since this user does not exist in the Windows VM ➜ the **Log In will Fail**

![azure portal](https://github.com/user-attachments/assets/ba4a924d-eb07-474c-8020-21e4ce0ad6d7)

Repeat the **Failed Log In** 2 more times with the same **Wrong Username & Password**

<br>

✅ So 3 Logs have been Generated on the Windows VM ➜ which we will analyse later

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>3️⃣ Generate some Failed SQL Server Authentication Logs against the windows-vm</h2> </summary>
<br>

> Remember that we installed the SQL Server Database in the Windows VM ➜ so we're going to attempt to log into it now.
> 
> Still within the **Attack VM**, we're going to install **SSMS** ➜ which we'll use to attempt to log into the SQL Server

<br>

Go back to the **Attack VM** > Using **Microsoft Edge** > You can **[Download SSMS through this link](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)**

Open the **SSMS-Setup-ENU.exe** File from the Downloads > **Install** it

![azure portal](https://github.com/user-attachments/assets/ef8cd4ad-57df-40d6-b5b3-abca01b901bc)

![azure portal](https://github.com/user-attachments/assets/3688d252-582b-4dd5-8cc9-8949bbd2f757)


>   <details close> 
>   
> **<summary> 💡 Note</summary>**
> 
> We're going to use this **SSMS** to **Connect to the SQL Server in our Windows VM**.
> 
> Once the **Installation is Completed** ➜ we'll take the **Windows VM's Public IP Address** (which is where the **SQL Server** is) ➜ and we're going to **Generate some Logs** by attempting to Log Into it as a bad actor.
> 
> And then at the end of this lab we'll log back into the **Windows VM** again and **Inspect the Logs**
> 
> We'll also log into the **Linux VM** and **Inspect the Logs** in there as well.
>   </details>

<br>

<h2></h2>

<br>

Now we're going to open **SSMS**:

![azure portal](https://github.com/user-attachments/assets/8be34c69-6216-4aa7-b2f3-d633f2d82c2b)

Then we'll copy the **IP Address of the Windows VM** ➜ which has the **SQL Server**

- in Server Name: we'll Paste the **IP Address**
- Authentication: **SQL Server Authentication**
- then our Real **Username** is ```sa``` & **Password** is ```Cyberlab123!```

  - but we're going to login with a user that doesn't exist ➜ so we can **Generate Failed Logs**

![azure portal](https://github.com/user-attachments/assets/edb1c19c-5959-4764-a183-c74a9e5d6e76)

We'll **Attemp and Fail** to Login 2 more times to **Generate a total of 3 Failed Logins**

Then we'll "Login For Real" with the correct **Username** & **Password** just to show that we can Login from **Australia (Attack VM)**

![azure portal](https://github.com/user-attachments/assets/f70930a6-50b1-49f5-8fad-7b56571eaed1)

We can then disconnect from the Server:

![azure portal](https://github.com/user-attachments/assets/8d0f297f-b5ba-4933-8597-903be7144b25)

  </details>

<h2></h2>

<details close> 
<summary> <h2>4️⃣ Generated some Failed SSH Logs against the linux-vm</h2> </summary>
<br>

> Lastly, we're going to induce some **Failed Authentications against the Linux Server**.
> 
> This will allow us to **Generate some Logs in our Linux VM** & and analyse them later as well

<br>

Going back to the **Azure Portal** > Go to **Virtual machines** > Open the ```linux-vm``` > copy its **Public IP Address** 

  ![VM create](https://github.com/user-attachments/assets/cf625627-4567-452b-b13e-d07a8f4cb4b9)

  ![VM create](https://github.com/user-attachments/assets/4668916b-869d-4065-8274-99063e0625ba)

Then inside the Attack VM ➜ we'll open **Powershell**

  ![VM create](https://github.com/user-attachments/assets/8426567a-374f-42f1-b2bf-bf01ad2c2f9e)

Now we can **SSH** ➜ use a **Fake Username** ```josh``` ➜ this Username doesn't exist on the **Linux VM**

So we'll type: ```ssh "FAKE USERNAME"@"DESTINATION (which is the Linux VM)"```  ➜ press "Enter" to attempt to connect:

  ![VM create](https://github.com/user-attachments/assets/7fb6f2d1-c152-43e3-b771-f1bc10716e71)

Type **"yes"** to Accept the Certificate:

  ![VM create](hhttps://github.com/user-attachments/assets/bf5dc7e4-245f-4864-b849-5105d1fa1b4d)

Then it'll ask us to **Enter our Password**

This **User doesn't even exist**: so whatever Password we put ➜ it's going to **Fail to Login & Create a Log**.

Basically we're attempting to **Brute-Force into the Linux VM**  ➜ do it 3 times to **Generate 3 Failed Logins**:

  ![VM create](https://github.com/user-attachments/assets/fab202f7-a7a0-4007-a1ae-df4dc01e9097)

Then we can actually Shut Down our **Attack VM** ➜ we won't be using it anymore for this Lab ➜ and go back to our own Computer

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

  </details>

<h2></h2>
<details close>
  
<summary> <h2>5️⃣ Inspect the Logs for RDP & SQL Server in the Windows VM</h2> </summary>
<br>

> So now we're going to connect back to the **Windows VM** ➜ take a look at the **Event Viewer** ➜ and look at the **Logs we Generated**
> 
> We'll do the same thing with the **Linux VM** after ➜ look at the **Logs we Generated** by attempting to Log into it

<br>

Inside our **Windows Vm** > Copy the **Public IP Address**:

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

Open **Remote Desktop** > connect to the **Windows VM**

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

Then open **Event Viewer**:

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

Again, this is where the logs are ➜ First we're going to check out the **"Security"** ones.

These are the logs for when someone attempts to **Connect with Remote Desktop** or even try to **Map a Remote File Share**.

Event ```4625``` is the **Failed Logon Event** ➜ and we can see a whole bunch of them here:

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

We didn't generate most of these.

If we **"Filter Current Log"** > and type ```4625``` just to see the **Failed Logons** ➜ it'll show us only the **Failed Logons**:

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

We can see the the **"Account Name"** is different for each Event ➜ and these are **actual bad actors or bots** on the Internet.

These are not our **Intentional Failed Logons** ➜ these are other randon entities trying to **Logon to our VM**, since it's been on for the previous 10 hours.

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

If we scroll down we can actually find **Our Own Failed Logon** with the **Username** ```josh```

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

⚠️ Aside from our 3 **Intentionally Generated Failed Logs**:
- We can see that there were almost **2000 different Attempts to Break Into our Windows VM**:

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

<br>

<h2></h2>

<br>

> Next, because SQL Server is actually installed on this Windows VM ➜ we're going to check the **"Application"** Logs.
> 
> In the Windows Event Viewer, the **SQL Logs** get registered in the **Application Tab** instead of the **Security Tab**.

<br>

Right away ➜  we can see our Successful Login with the User ```sa```:

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

We're also able to see the **Failed Logins** into the **SQL Server** using the made up **Username** ```josh``` ➜  which does not exist in the **Windows VM**:

  ![VM create](https://github.com/user-attachments/assets/6922b5a7-bf0e-46b9-ac0b-b79be1074f97)

<br>

>   <details close> 
>   
> **<summary> 💡 Summary</summary>**
> 
> From our own Computer ➜ we **RDP into the Windows VM**. 
> 
> We then inspected the **Login Failures and Successes** for both **RDP** as well as the **SQL Server**.
> 
> We looked at the event IDs:
>   - **4625** for the **RDP Failed Logins**
>   - **18456** for the **Failed Logins for the SQL Server**
> 
>   </details>

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>6️⃣ Inspect the Logs for the Linux VM</h2> </summary>
<br>

> The next thing we're going to do is **Login to the Linux VM**.
> 
> And then we're going to take a look at the **Failed Logs** and finish this lab.

<br>

To **Attempt to Connect to the Linux VM** ➜ we first need the get the **IP Address of the Linux VM**:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

From our Computer:
- if you're using a **Mac** ➜ **open Terminal**
- if you're using **Windows** ➜ **open Powershell**

And type in the following:

```commandline
ssh labuser@PUBLIC IP ADDRESS OF THE LINUX VM
```
<br>

Press **"Enter"** > then type the **Password**: ```Cyberlab123!``` > then press **"Enter"** again

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

We are now Logged Into the **Linux VM**

To see the **Logs** we can type in the following **Linux Command**:

```commandline
cd /var/log
```
<br>

☝️ This will basically change our Directory to the **Log's Directory**

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

And then we can type ```cat auth.log | grep password``` to pull out all the "lines" that have the word **"Password"** in it:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

We can see a whole bunch of **Failed Password for Invalid User** Events:
- Meaning ➜ some entities were trying to **Login to our Linux VM** from **Random IP Addresses** using **Wrong Credentials**.

We can also see our **Successful Logins** using the **Username** ```labuser```:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

If we filter through:
- ```Accepted``` ➜  we can see that only we were able to **Successfully Login**:

```commandline
cat /var/log/auth.log | grep Accepted
```
<br>

- ```josh``` ➜  we can see all the **Unsuccessful Login Attempts** with the Username "josh":

```commandline
cat /var/log/auth.log | grep josh
```
<br>

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)


✅ Welcome to Cybersecurity!


  </details>

<br>

<br>

<br>

# Conclusion


We have to keep in mind hat if we have, let's say, 1000 computers in our Environment ➜  it would be too laborious to just login into each one of them, and analyse the each log indivudually this way.

That's why we are going to **Automatically Ingest all the Logs into a Central Repository**.

We'll Set Up Queries to Automatically look through the Logs and Alert us on certain Events.

This is the end of our **Logging and Monitoring Lab**, and in the next phases we're going to Inspect Logs, Create Incidents & Respond to those Incidents.

<br />

<br />

<br />  

<br /> 

<br />

<br />  

<br /> 
 
