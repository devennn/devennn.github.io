---
title: "Streamlining AI conversation flow"
date: "April 13, 2023"
excerpt: "Streamlining AI conversation flow"
cover_image: "https://images.pexels.com/photos/8762286/pexels-photo-8762286.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1"
project: ""
---

As I add capabilities to my chatbot, it becomes harder and harder to build a central memory to hold all the past conversations. 

Why should history be cached? Because I want to take advantage of the GPT3 capability to refer to remembered pass information and answer based on that. The thing is, intent sometimes fails. When intent fails, it is hard to handle that failure path. As humans, when we fail to make sense of the connection between current information and the past (especially if the intent spans more than three conversations), we ask or we guess. If we choose to guess, we did it based on past information, not random information.

Because of this, I redesigned the chatbot to have a central memory that stores everything in sequential order. The idea is that if the intent recognition fails to predict the right intent, GPT3 can reply based on what happened before. Refer to the image below.

![Assistant](https://user-images.githubusercontent.com/49480914/231687028-757931ec-577c-460e-a001-62d0bebcaded.jpg)

After action is performed, the reply generator will generate human-readable text, which will be updated to the main conversation thread.

See one example below.

![Capture](https://user-images.githubusercontent.com/49480914/231687666-eb0a1645-9dc0-4df0-97fb-2c5cf4eb04ca.PNG)

I was asking some questions about running, and then a reminder (scheduled before) was sent. I am rescheduling it for 4 p.m., but the intent fails to recognize it. And it gives an output that basically says, "I'm not sure what you mean. Are you trying to reschedule the run?" I then retry again to reschedule adding more information, which hopefully helps the intent recognizer to predict correctly. And it successfully performed rescheduling. 

If each text and intent were treated as a single process, this would give an ugly output. 

But of course the caveat for this method is that if the first text's intent fails, then there is no reference to previous text, which a human in that situation will ask for clarification, but this will just give nonsense output.
