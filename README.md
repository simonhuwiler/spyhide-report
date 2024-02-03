# SpyHide-Report – eine Recherche zu einer iranischen StalkerWare 
## Abstract
SpyHide was a stalkerware from Iran. It was hacked by a hacktivist in 2023. This article looks at this stalkerware, how it worked and who was behind it. The report is aimed at a technically interested audience and is intended to facilitate further investigations. The research served as a basis for [this article for the Swiss Newspaper «Neue Zürcher Zeitung»](https://www.nzz.ch/meinung/es-braucht-einen-richtungsentscheid-ld.1777119).

## Introduction 
Stalkerware or spouseware are surveillance apps that can be bought for just a few dollars. Technologically, they are not comparable with products from state actors. They are simpler in design and hardly exploit any gaps in smartphones. The surveillance operator needs access to the target's smartphone in order to install the app. And that is what makes them so dangerous: warnings on the mobile phone can be clicked away by the perpetrator, and security measures and malware scanners can even be deactivated.  

Many providers position their app in the parent-child market ("parenting control"). The app is supposed to be used to monitor the child's mobile phone use. In reality, however, the app is used by others: people who want to monitor their nearest and dearest, usually their partner. 

In 2023, a major provider from Iran was hacked by the Swiss hacktivist [maia arson crimew](https://maia.crimew.gay/) (lower case according to maia). The data was published on the hacktivist platform DDOS Secrets for journalists and scientists. 

It is recommended to read [maia's analysis first](https://maia.crimew.gay/posts/fuckstalkerware-2/).

## The attack

maia calls her series of articles "#FuckStalkerware". She writes about StalkerWare providers whose data is leaked to her or she herself is attacked. In 2023, SpyHide became her target. Her [blog post](https://maia.crimew.gay/posts/fuckstalkerware-2/) explains how she went about it. She describes it as surprisingly simple. In a nutshell, SpyHide had two vulnerabilities: 

1. **git exposure**: the makers of SpyHide copied the git directory (.git) to the server. This allowed maia to access the source code and analyse it for further vulnerabilities 
2. **image upload**: he spyware can transfer images from the target device to the control server. This receives the image and saves it to the file system without checking the file format. A reverse shell could thus be infiltrated via the API of the control server, allowing access to the entire server. 

![Screenshot of maias blog post](images/blog.png)
<small>Screenshot of maias blog pos</small>

## What data is available? 

The data published by Maia at DDOS-Secrets contains: 

* The backend of SpyHide, written in PHP 
* MySQL/MariaDB databases of the backend and old backups of them 
* Wordpress instances of the sales website and associated databases 
* Images copied from target devices 
* Phone recordings from target devices (but the data is corrupt) 
* Microphone recordings from target devices 

Images and audio recordings from target devices are deleted by SpyHide itself after some time. According to the SpyHide backend after three months. However, the metadata remains in the database. SMS messages were also deleted. However, a backup copy from 2019 extends the database by a few more years. 

![database UML](images/dbschema.png)
<small>The database schema of the control panell</small>

## Data insights 

SpyHide operated with two databases: `admin_spyhide` (first login 2015-08-14) and `admin_spyhidetempbackup23` (first login: 2016-11-01). Even though the structure of the databases is identical, they contain different and both current data. When logging in, the control server first checks the password on the first database. If the login fails, the credentials are tried on the second database. From mid-2018, new users were only registered on the newer database `spyhidetempbackup23`. `admin_spyhide` remained active, however, with the last logins dating from 13 July 2023. 

Presumably two SpyHide offshoots were initially operated: One for the Iranian market, one for the international market. This is indicated by different domains (more on this later). It is not known why the data was never merged. 

The two databases show that 
* **96,462 devices** were monitored 
* **857,694 users** have registered. Only very few of them paid. 
* **3.3 million SMS messages** were recorded 
* **1.9 million GPS coordinates** stored 
* **1.2 million calls** recorded 
* **380,000 images** copied 

The databases receive various information on payments. It appears that the payment provider was changed several times. Payment data mainly ends up in the `spy_orders` and `spy_direct_payment` tables. Adding up all verified payments ("verified" status), SpyHide has collected 717,202 dollars. This does not include payments in Iranian riyals. Its exchange rate is so low that these payments - despite their large number - are not significant.

## Hands-on: What SpyHide can do, what it looks like 
The control server can be restored with a little effort - even if the source code is chaotic and has redundancies. SpyHide collects: 

* general mobile phone data (operating system, settings, etc.) 
* GPS history 
* SMS Messages
* calls and the option to record them 
* photos 
* contacts 
* installed apps 
* background recordings ("Ambient") of 1 - 5 minutes. 
* Browsing history (but to a limited extent) 

Rooted devices can also collect the following according to the backend: WhatsApp, Facebook, Hangouts, Skype, Line, KiK, Viber, ChatOn and Gmail. However, there is no table in the database for any of these services. This was probably just an empty promise. The developers also seem to have experimented with Telegram. The database contains Telegram messages between March 2015 and February 2016 from 15 different devices. Most of the messages are in Persian. It is quite possible that the creators monitored themselves for testing purposes. 

The backend for customers is relatively simple. The active devices and their status are displayed in an overview. The SpyWare can be downloaded directly from there as an APK. The individual tabs show further information on the collected data. See the image gallery. 

<img src="images/dashboard.png" width="30%"></img> <img src="images/sms.png" width="30%"></img> <img src="images/call.png" width="30%"></img> <img src="images/map.png" width="30%"></img> <img src="images/ambient.png" width="30%"></img> <img src="images/images.png" width="30%"></img> 

# The SpyApp - remote installation really possible? 

[In a cinematic trailer](https://www.youtube.com/watch?v=1N98WJrZ2Yk ), SpyHide claims that the SpyWare can be installed without physical contact. This is strongly questioned. 

SpyHide offers two apps. The conventional app is installed on the target device by the attacker himself. As of August 2020, the app has been extended so that it can be installed by the victim themselves. For this purpose, the website "tesla-ringtone.site" was launched as a front. The attacker is supposed to encourage the target to download a ringtone app there. After installation, the app requests an activation code, which is provided by the attacker and connects the target device to his account. 

> «Spyhide remote tracker app is in fact a Ringtone Creator application, but within that is embedded  Spyhide tracking application, the tracker app is completely hidden in Ringtone Creator application, so that you can introduce this application as a professional Ringtone Creator to your target person and encourage him/her to install this application on his/her cell phone.»

This probably did not work. Even old Android devices warn of a "virus" several times. In order to be able to install third-party apps at all, the security settings of the mobile phone must also be changed. All possible authorisations are also requested during installation. It takes a lot of good faith to install this app.

<img src="images/app_2.png" width="18%"></img> 
<img src="images/app_3.png" width="18%"></img>
<img src="images/app_4.png" width="18%"></img>
<img src="images/app_5.png" width="18%"></img>
<img src="images/app_6.png" width="18%"></img>
<img src="images/app_7.png" width="18%"></img>
<img src="images/app_8.png" width="18%"></img>
<img src="images/app_9.png" width="18%"></img>
<img src="images/app_10.png" width="18%"></img>
<img src="images/app_11.png" width="18%"></img>

As soon as the activation code has been entered, the app icon disappears from the home screen. The app also changes its name to "com.wifisettings.editor". The app logs on to the server via POST request using the address http://virsis.net/client/scripts/service_new.php (more on Virsis later). The server is now offline.
```json
{  
    "action": "createCustomAccount",  
    "deviceId": "861349103823194",  
    "deviceName": "HONOR FRD-L09",  
    "devicePhone": " ",  
    "password": "183251",  
    "tempCharge": "custom",  
    "username": "183251"  
 } 
```

Über Anrufe auf die Nummer `#3333*` oder `#3838*` lässt sich die App auf Zielgeräten öffnen. 

## A StalkerWare is created: From SpyHide to Virsis
The story of SpyHide begins sometime in 2015, when Mohammad A. works as a developer for a company that creates websites and apps. It is not known when he decided to focus on spyware. The following dates are verified. It is not always necessarily the date of registration, but can also be the date when the entry was captured by crawlers. 
|Date|Action|
|-----------------|:----------------|
|2015-07-09|**spyhide.com**<br /> Mohammad A. registers spyhide.com in his name and address. IP: 88.198.136.80|
|2015-08-14|**Oldest entry in the database**<br />First (test) entry in the database (user mojmadah@yahoo.com.ollllllllllllld, password 123456)|
|2015-09-17|**virsis.net**<br />Virsis.net is registered by Mohammad A.. Virsis offers services for search engine optimisation, web design and app development. However, the control server runs on the Virsis server until 2023. Whether the company has ever offered legitimate work is questionable. No former employees could be found. |
|2016-02-17<br />2016-02-18|**Relocation to Germany**<br />Spyhide.com and virsis.net move to Germany to a server of the company Hetzner (144.76.1.163). SpyHide will remain there until Hetzner blocks it in 2023. |
|2016-03-20|**spyhide.ir**<br />The Iranian domain of SpyHide is registered. It also points to the German server.|
|2016-03-28|**Obfuscation**<br />Spyhide.com is obfuscated. New email address: spyhide.com@gmail.com|
|2016-06-23|**More domains**<br />Mohammed A. registers further domains in his name. At least they remain active for a long time:<br />best-mobile-tracker.ir, android-tracker.ir, Bestcellphonetracker.ir, firstme.ir, Netvaght.ir, Spycellphone.ir 


### 2015-08-14: Oldest entry in the database 
First (test) entry in the database (user mojmadah@yahoo.com.ollllllllllllld, password 123456) 

2015-09-17: virsis.net 
Virsis.net is registered by Mohammad A.. Virsis offers services for search engine optimisation, web design and app development. However, the Virsis server runs the control server until 2023. Whether the company has ever offered legitimate work is questionable. No former employees could be found. 

2016-02-17/2016-02-18: Relocation to Germany 
Spyhide.com and virsis.net move to Germany to a server of the company Hetzner (144.76.1.163). SpyHide will remain there until Hetzner blocks it in 2023. 

2016-03-20 spyhide.ir 
The Iranian domain of SpyHide is registered. It also points to the German server. 

2016-03-28: Obfuscation 
Spyhide.com is obfuscated. New mail address: spyhide.com@gmail.com