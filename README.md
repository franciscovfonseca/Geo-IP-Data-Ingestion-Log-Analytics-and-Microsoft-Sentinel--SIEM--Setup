<br>

<h1 align="center">Log Analytics Workspace & Microsoft Sentinel (SIEM) Setup</h1>

<br>


![Banner](https://github.com/user-attachments/assets/1cc4caf7-8f6c-4747-aaeb-eeee80212ba0)
<br />
<br />

In this lab we'll set up **Log Analytics Workspace** as our **Central Log Repository****, as well as **Microsoft Sentinel** as our **SIEM**.

Basically we're going to create what we call a **"Watchlist"** inside of our **SIEM** (Microsoft Sentinel).

This Watchlist is going to essentially be a long list of **Network Blocks**, with the corresponding *Longitude*, *Latitude*, *City Name* and *Country Name*.

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
<summary> <h2>4️⃣ Create the Geo IP Watchlist</h2> </summary>
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

⚠️ There are about 26k rows/records

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

<h2></h2>

<br>

While the Geo IP CSV File is being "uploaded", we can actually start quering the Watchlist inside of Log Analytics.

Copy this syntax : ```_GetWatchlist("geoip")```

We'll open the **Log Analytics Workspace** we just created ➜ our Central Log Repository:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

And now we'll see some of the records inside ➜ click on "Logs"

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

<br>

>   <details close> 
>   
> **<summary> 💡 Note</summary>**
> 
> There's not really anything in here yet ➜ because we haven't sent anything here yet.
> 
> But when we ingest the Watchlist into Sentinel ➜ Sentinel makes use of the LAW and stores the Watchlist in there.
> 
> And just to clarify: ```_GetWatchlist("geoip")``` is the syntax we use to query the Watchlist
> 
>   </details>

<br>

Now if we run the Query ➜ once it starts ingesting ➜ a few records should come out here:

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

✅ So we were able to Query it and see a few  different records inside

<br>

<h2></h2>

<br>

So after waiting a bit  ➜ if we go back to the **Watchlist** ➜ we can see that all the 26k records were successfully uploaded as **Watchlist Items** ✅

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

Now go back to **Log Analytics Workspace** > go to **"Logs"** again > and **Run this Query**:

```commandline
_GetWatchlist("geoip")
| count
```
<br>

![azure portal](https://github.com/user-attachments/assets/4032bf0f-95dd-4edd-a1a2-c714da69ba61)

✅ We can see 26k+ records were "counted" as being part of the Watchlist

<br>

<h2></h2>

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
 
