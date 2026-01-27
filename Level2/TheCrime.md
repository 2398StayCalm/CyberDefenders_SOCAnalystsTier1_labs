# 👷The Crime
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/the-crime/]

### 📓 Scenario:
We're currently in the midst of a murder investigation, and we've obtained the victim's phone as a key piece of evidence. After conducting interviews with witnesses and those in the victim's inner circle, your objective is to meticulously analyze the information we've gathered and diligently trace the evidence to piece together the sequence of events leading up to the incident.

### ❓Questions❓:

#### ❔  1: Based on the accounts of the witnesses and individuals close to the victim, it has become clear that the victim was interested in trading. This has led him to invest all of his money and acquire debt. Can you identify the SHA256 of the trading application the victim primarily used on his phone?

We're starting from downloading a zip file with all data that's necessary for this lab. We unpack it and check what's inside.

<img width="647" height="341" alt="image" src="https://github.com/user-attachments/assets/16662fe2-cf2c-404c-a888-f59729bf69c8" />

As we can see there is extracted application filesystem. In this task they ask us to provide a SH256 hash of the trading application. Time for a little research. Let's go to the "app" directory. What we know is that there is a trading application and one of two directories looks like it has something to do with that ("trade" in its name). There! We can see that it has a base.apk file which is an Android package, there should be our hash.

<img width="642" height="265" alt="image" src="https://github.com/user-attachments/assets/baef77de-b149-4103-bb3b-bc3ba07e9aea" />

It worked! 🔥

#### ❔  2: According to the testimony of the victim's best friend, he said, "While we were together, my friend got several calls he avoided. He said he owed the caller a lot of money but couldn't repay now". How much does the victim owe this person?

After doing some research we established that it is possible that there are some database files from the telephony providers with a ".db" extension. Let's check if we can find some data with messages.

<img width="835" height="63" alt="image" src="https://github.com/user-attachments/assets/3e3db2e1-7608-48de-bf77-25ea0a948815" />

There they are. As a result we got two files, from which one of them is correlated with providers. Now to read this file we can use many tools, but I suggest using sqlitebrowser, so it can be more readable with GUI. When we open this DB file we can go to the Browse Data tab so we can choose a table of our interest. We'll check "sms" table. There we have only one message that could guide us to our answer.

<img width="1253" height="318" alt="image" src="https://github.com/user-attachments/assets/aaf05db8-8bbe-4809-bdb9-a92bcfc545e3" />

It worked! 🔥

#### ❔  3: What is the name of the person to whom the victim owes money?

Again I suggest using sqlitebrowser. This time we're looking for the name of a person that our victim owes money. From victim's best friend statement we know that this person tried to call the victim several times and was avoided. Let's check if there are any logs which could tell us about contact from the victim's phone. There, we even found a calllog.db which can potentialy show us who, when and how often called the victim.

<img width="908" height="151" alt="image" src="https://github.com/user-attachments/assets/fb554ac8-0c46-4b52-a5e3-748c90ecc6a6" />

Okey, since we have this DB file, let's deploy our tool to dig deeper. Once the file is open we can see a "calls" table which would suggest that there are some interesting info. We go to the Browse Data tab again and choose this table. Once we sort the table and get rid of empty ones we can discover very interesting stuff. First we got different numbers, duration and names of callers. I assume that these are names from contact list of our victim. From the last question we know that the victim owed money in Egyptian currency and since all callers were calling from Egypt that is not a great hint. But remeber that the person that we are looking for, tried to call several times and the victim avoided those calls. Lets check Duration column. There! We can see many positions where duration time equals 0 so it means there was no conversation. Many of them were from the same person :)

<img width="737" height="468" alt="image" src="https://github.com/user-attachments/assets/5fbc05ce-1dca-4c97-b246-61ba000ed39d" />

It worked! 🔥

#### ❔  4: Based on the statement from the victim's family, they said that on September 20, 2023, he departed from his residence without informing anyone of his destination. Where was the victim located at that moment?

Okey this one actually took some time to figure out. We know that we need to look for some location which would suggest some GPS-related files. Now I've done some research but that was a dead end. Same with information about WiFi connections etc. What left is files related to maps. Which means that if a user used Android it's possible that he used Google-maps. So I've done research there also - still nothing. BUT ... there are sometimes system snapshots. While the files are system-generated, they are triggered by normal UI behavior, such as: App goes to background, User switches apps, User opens the Recent Apps / Overview screen, System needs a thumbnail for task switching. So let's check if there are some snapshots made by Android 🙏.

<img width="652" height="173" alt="image" src="https://github.com/user-attachments/assets/0647da22-4292-4403-9ecc-17f9a8178ff2" />

There they are ❗ Now let's go to this directory to check if there are some snapshots from Google Maps. YUP GOT IT ❗

It worked! 🔥

#### ❔  5: The detective continued his investigation by questioning the hotel lobby. She informed him that the victim had reserved the room for 10 days and had a flight scheduled thereafter. The investigator believes that the victim may have stored his ticket information on his phone. Look for where the victim intended to travel.

If the victim stored his ticket information on his phone, it's possible that somwhere there is downloaded ticket. Let's do the research! It would be nice if we could find Download directory. This requires som digging ;)

<img width="881" height="182" alt="image" src="https://github.com/user-attachments/assets/aaf299c4-17cb-46eb-bdf9-c47e6a122262" />

It worked! 🔥

#### ❔  6: After examining the victim's Discord conversations, we discovered he had arranged to meet a friend at a specific location. Can you determine where this meeting was supposed to occur?

Here we have to look in the system_ce directory once again (directory which is available only after user unlocks the phone). We can assume that some message logs can be visible only somwhere here. So let's search for some file related to discord in that directory.

<img width="871" height="132" alt="image" src="https://github.com/user-attachments/assets/3ac84459-9a0f-4482-8080-fbcf6f4194d7" />

We can see something is here, but that's only a animated picture of a sweet cat. No use of that. But we won't give up, we gotta dig deeper. Actually the picture that we found is to far, we can go back 2 directories up to shortcut_service. There is a file called shortcut.xml which is very useful in terms of using Discord for example and that's what we need. It is Android system metadata and it's useful for understanding what the user did and what apps he/she interacted with. Right now it is time to reveal what is inside.

<img width="1268" height="816" alt="image" src="https://github.com/user-attachments/assets/57b14055-048d-4a2d-b95d-7277a5ca7026" />

As you can see there is a lot of information, so let's check if there is anything about Discord activity. After some filtering we got it!

<img width="1263" height="793" alt="image" src="https://github.com/user-attachments/assets/39eb056c-3210-4513-98e1-fa817fbb4888" />

It worked! 🔥

We've done it! 🥇
