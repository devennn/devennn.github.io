---
layout: post
title: Simple wordcloud to understand Malaysian COVID19 tweet
published: false
---
This simple project is to explore an open source Twitter dataset on COVID19. I am interested only for Malaysian tweets.

Full dataset:
- [Coronavirus (covid19) Tweets](https://www.kaggle.com/smid80/coronavirus-covid19-tweets)

The original dataset combines tweets from other countries. I have filter the tweets based on county_code = 'MY'. This gives me 4000+ lines of tweets to play with. 

## Exploring raw dataset

'text | retweet_count | favourites_count | created_at | lang\n---- | ---- | ---- | ---- | ----\nWhen will this be over #CoronavirusOutbreak | 0 | 12523 | 2020-03-05T03:19:35Z | en\n"I miss those days when I sneeze people would politely say ""Bless You"" now people just say ""Get the fuck out of here""\n#coronavirus #CoronavirusOutbreak" | 1 | 5107 | 2020-03-05T09:54:45Z | en\nBond movie postponed cuz nobody wants to die. #JamesBond #coronavirus #CoronavirusOutbreak | 0 | 5107 | 2020-03-05T11:47:07Z | en\n"All these days software was scanned for virus, now software engineers are scanned for virus.\n#coronavirus #CoronavirusOutbreak" | 0 | 5107 | 2020-03-05T13:27:55Z | en\n"#GempakKongsi Maklumat terkini mengenai #Covid_19 sehingga Jam 6pm Petang tadi! Stay safe guys! Jangan lupa untuk utamakan kesihatan ok? 💪🏻 \n\nInfo &amp; 📸: @KKMPutrajaya \n\n#Gempaknews\n#GempakKongsi https://t.co/kiLAKRJQjv" | 8 | 870 | 2020-03-05T14:46:20Z | in\n"Malaysia’s Covid-19 update:\n05 March 2020\n- 55 total cases\n- 22 recovered\n- 33 currently under treatment \n\nOut of 33 cases, 21 cases are the 1st generation contact from Case 26, while 10 cases are the 2nd generation contact of Case 26.\n\n@KKMPutrajaya #covid19 #CoronavirusOutbreak https://t.co/OQruT9Zrx1" | 0 | 496 | 2020-03-05T15:16:25Z | en\nPlease come forward if you’re one of these contacts. Stop #CoronavirusOutbreak https://t.co/hNi3TpbcDl | 0 | 432 | 2020-03-05T16:15:57Z | en\n"Susulan COVID-19 kerajaan Arab Saudi telah mengambil langkah pencegahan 😔 Ya Allah..Ya Allah Yang Maha Melindungi, lindungi kami daripada wabak penyakit ini\n #haramain #kaabah #makkah #madinah #masjid #masjidalharam #masjidilharam #masjidnabawi #CoronavirusOutbreak #covid19 https://t.co/X3xaaYNuNB" | 0 | 11692 | 2020-03-06T00:32:52Z | in\n#Iran did not reveal the #CoronavirusOutbreak until a deputy minister got it. After that it allowed #SaudiArabia cotizens who traveled to the infected region to go back but did not stamp their passports. Some were tested positive in Saudi. This is bad #PandemicPreparedness #virus https://t.co/9Ri94bphog | 0 | 975 | 2020-03-06T22:25:51Z | en\nThey are among the 145 recorded fatalities due to the #CoronavirusOutbreak in #Iran https://t.co/sIGfzi9whT | 0 | 8314 | 2020-03-07T22:17:28Z | en\n'


## Cleaning

## Plotting the wordcloud

## What can I understand?
