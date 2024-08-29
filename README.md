<br>

<h1 align="center">Log Analytics Workspace & Microsoft Sentinel (SIEM) Setup</h1>

<br>

<p align="center">
<img width="600" src="https://github.com/user-attachments/assets/49ac1ae0-6f3c-46c8-9f04-624727d5ccbe" alt="Banner"/>
<br />

<br />

In this lab we'll set up **Log Analytics Workspace** as our **Central Log Repository**, as well as **Microsoft Sentinel** as our **SIEM**.

Basically we're going to create what we call a **"Watchlist"** inside of our **SIEM** (Microsoft Sentinel).

This Watchlist is going to essentially be a long list of **Network Blocks**, with the corresponding *Longitude*, *Latitude*, *City Name* and *Country Name*.

And basically we'll use this to derive **Geo-location from Attacker's IP Addresses**.

So when somebody **Attacks our Network**, or for example they fail to login to our Virtual Machine, we'll be able to:
1. Inspect that Log
2. Match the Log to a given Network Block
3. and Plot it on the World Map

This way we'll be able to clearly see **Where the Attacks are Coming From**, according to their **Geo-Location**.

<br>

<br>

<details close> 
<summary> <h2> 1️⃣ Download the Geo-Data File</h2> </summary>
<br>

The first thing we're going to do is **[Download this Geo-Data CSV file](https://github.com/joshmadakor1/Cyber-Course-v2/blob/main/Sentinel-Maps(JSON)/geoip-summarized.csv 
)** onto our Desktop.

It will open up a new tab, and you can **Download the Raw File**:

![azure portal](https://github.com/user-attachments/assets/77c4197d-2525-4737-b3b3-c6861ba79def)

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

![azure portal](https://github.com/user-attachments/assets/b2c33e62-f92c-42c9-bcd7-8d1200ff1108)

We'll click **"Create log analytics workspace"** and input the following details:
- Resource group: ```RG-Cyber-Lab```
- Name: ```LAW-Cyber-Lab-01``` ➜ ⚠️ it has to be globally unique
- Region: ```East US 2``` ➜ put it in the **Same Region** as the other resources from our environment

We can then just click **"Review + Create"**:

![azure portal](https://github.com/user-attachments/assets/c51e8a9e-3f59-4f8b-880c-f8ccaa93f4a9)

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

![azure portal](https://github.com/user-attachments/assets/b6477453-2064-4cc5-9e5e-fd1a5689bf7a)

Click **"Create Microsoft Sentinel"**:

![azure portal](https://github.com/user-attachments/assets/eed9d9c9-69ac-446e-b220-42f6e61512e1)

This next step is when we Add the Log Analytics Workspace we just made to our Microsoft Sentinel instance:

- We'll click on our **Workspace** > and click **"Add"**:

![azure portal](https://github.com/user-attachments/assets/20808b86-f860-410d-9391-4e5dada50ab5)

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

![azure portal](https://github.com/user-attachments/assets/ae218a11-3876-467b-8c3e-c690d6a163a6)

Then we'll go to **"Watchlists"** on the left ➜ and we're going to **Create a New Watchlist**

![azure portal](https://github.com/user-attachments/assets/b968a96f-c314-4e56-8271-f4bf7c8f45e6)

Now for the Geo IP Watchlist we'll use this details:
- **General Tab**:
  - Name & Alias: ```geoip```

![azure portal](https://github.com/user-attachments/assets/f1f57b45-c0ab-4209-b4dc-cc47faacc779)

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

Click **"Review + Create"** to Create the ```geoip``` Watchlist:

![azure portal](https://github.com/user-attachments/assets/5a5b6d9b-cc8c-4c25-a69e-ec0ccf6cfd37)

This should take a while:

- Allow time for these files to “upload” from your computer into Sentinel / Log Analytics Workspace.

⚠️ There are about 26k rows/records

![azure portal](https://github.com/user-attachments/assets/9cbb81b3-04e4-487b-a32e-88b9ca046805)

<h2></h2>

<br>

While the Geo IP CSV File is being "uploaded", we can actually start quering the Watchlist inside of Log Analytics.

Copy this syntax : ```_GetWatchlist("geoip")```

We'll open the **Log Analytics Workspace** we just created ➜ our Central Log Repository:

![azure portal](https://github.com/user-attachments/assets/565cb732-005c-491a-a7dd-429902c54009)

And now we'll see some of the records inside ➜ click on **"Logs"**

![azure portal](https://github.com/user-attachments/assets/8955d47a-b11f-4820-a162-ac44f57743fc)

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

![azure portal](https://github.com/user-attachments/assets/e2fae7ef-54cc-4cf6-a502-26f32efcf984)

✅ So we were able to Query it and see a few  different records inside

<br>

<h2></h2>

<br>

So after waiting a bit  ➜ if we go back to the **Watchlist** ➜ we can see that all the 26k records were successfully uploaded as **Watchlist Items** ✅

![azure portal](https://github.com/user-attachments/assets/c518e72e-3304-48d0-a08e-f7481ffee9a9)

Now go back to **Log Analytics Workspace** > go to **"Logs"** again > and **Run this Query**:

```commandline
_GetWatchlist("geoip")
| count
```
<br>

![azure portal](https://github.com/user-attachments/assets/dab019eb-8aa8-495f-9b48-944f17f6613a)

✅ We can see 26k+ records were "counted" as being part of the Watchlist

<br>

<h2></h2>

  </details>

<br>

<br>

<br>

# Summary

<br>

In this lab we created a **Log Analytics Workspace**, which is our **Central Repository**.

We also created our **SIEM Instance**, which is **Microsoft Sentinel** ➜ and connected it to the **Log analytics Workspace**.

And then we ingested the **Geo IP Data Records** into our **Sentinel Watchlist**, and later we'll make the **World Maps** using that.


<br />

<br />  

<br /> 

<br />

<br />  

<br /> 
 
