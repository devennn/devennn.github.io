---
title: "Building my own AI Assistant"
date: "January 1, 2023"
excerpt: "Sharing the birth of my own AI Assistant"
cover_image: "https://cdn.pixabay.com/photo/2021/12/13/14/40/artificial-6868378_960_720.png"
project: ""
---

2022 was a year of building for me. I was busy creating all sorts of AI-powered software for freelance jobs and personal projects. But most of my personal projects end up in the side project graveyard.

Take a look at some of my past projects:

- Auto Image classifier to categorize my gallery photos? DEAD. Image classifiers are too expensive to run.
- Personal collection of NLU API for freelance projects? DEAD. I needed a bigger server to serve all customer projects and the ROI just wasn't there.
- Twitter scrapper for scraping famous tweets by states in Malaysia? DEAD. There were just too many moving parts to maintain and location ended up becoming irrelevant to the topic of interest.
- Pen Sakti, a SAAS for AI content generation? HALTED. Development stopped when ChatGPT was launched. I decided to pivot and use it as a newsletter to share news, tips and tricks on using Generative AI.

But out of all of these failed projects, there's only one that's still standing and that I use regularly - my AI Assistant to schedule tasks. My idea is simple: I want to schedule events or reminders using natural language. For example, "Remind me to buy oranges at 5pm today" or "Cash in check at 630pm."

It's a very tailored experience to my needs:

1. I don't like writing long text. So, the AI should be able to understand my query.
2. I want to make it conversational, not filling out forms. If there's missing information, just let the AI know.
3. I use WhatsApp as my main mode of communication, so I wanted something integrated with it.

And that's how the Assistant was born.

<img src="https://user-images.githubusercontent.com/49480914/211729088-e9792688-d2d5-4472-8618-16a643eabdcf.PNG" width="600" height="250">

At a high level, this is how it works:

![Assistant-Whatsapp](https://user-images.githubusercontent.com/49480914/211731052-ff8410a6-d3c4-4ba6-8e83-ee37827b60b8.jpg)

### Whatsapp App as UI

WhatsApp is my main communication app and I didn't want to add another chat app just for this one purpose. 

To build the backend, I used a Javascript library called venom-bot - https://github.com/orkestral/venom. It's good enough to handle basic text chat, but there are limitations. Being a free library, support is slow and I can't send buttons for now, even though the function exists.

### NLU Engine

AI is used in two areas: intent recognition and entity extraction. I used GPT-3 as the engine. It's incredibly powerful. The text is first classified as an intent, then time and subject entities are extracted.

Open AI API isn't free, but it's cheap for personal use. I used the same API to develop Pen Sakti, which has 20 active users. My monthly payment is still under $3.

### Actions

Currently, my assistant has two actions:

- Schedule an event on Google Calendar
- Send an event reminder at the specified time

The second action is easy to create. All events are treated as threads. Each thread will sleep until it's time to send a reminder. However, there's a limitation of 30 days for sleep time. Reminders won't work for events that go beyond that time frame, but they'll still be scheduled on Google Calendar.

Building the Google Calendar service was a bit tricky. I had to play around with the Google Console and permission settings. Since only a few people use this app, I didn't have to go through the whole process of publishing the app on Google, adding them as testers was enough.

### Deployment

Since all of these services are built as single services, I have the flexibility to deploy them to multiple clouds. I experimented with a few combinations:

- Home server + Digital Ocean - Sometimes the latency was high, and handling different OS versions and integrations was a headache. I passed on this option.
- Docker on Home Server + Docker on Digital Ocean - I had problems with the javascript app not printing QR codes for WhatsApp properly on the Docker Terminal. I passed on this option too.
- Kamatera with Ubuntu Desktop Image - I just wanted to have a good life, and deploying on one server was the easiest option for me. Plus, I can track memory consumption and do automated reporting better. I ended up deploying it on a 2Gb Ram, 1 CPU server for $5 a month. Not bad, and I could even go cheaper. But having more resources gave me more space to innovate.

### The joy of building

Building something that is beneficial to you and your family is incredibly fulfilling.

<video controls>
    <source src="https://user-images.githubusercontent.com/49480914/211730325-13cd1b6f-b5c2-49d6-844a-0a496f7dfe4d.mp4" type="video/mp4">
</video>



I'm already thinking of ways to extend the functionality of my AI Assistant. I have a few ideas in mind, like:

- The ability to schedule events from a poster using OCR technology
- The ability to reschedule events by replying to an old image or sending new text
- Or, new intents like the ability to save notes

The possibilities are endless! I can't wait to see what else I can come up with and how I can continue to make my life easier with this handy little assistant. Let's see where this journey takes us!
