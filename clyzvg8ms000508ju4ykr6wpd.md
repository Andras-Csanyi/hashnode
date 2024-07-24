---
title: "Slow is smooth, smooth is fast"
seoTitle: "Imposter syndrome"
seoDescription: "Imposter syndrome"
datePublished: Wed Jul 24 2024 13:19:47 GMT+0000 (Coordinated Universal Time)
cuid: clyzvg8ms000508ju4ykr6wpd
slug: slow-is-smooth-smooth-is-fast
tags: imposter-syndrome

---

Keep Calm Bro, unless you want to make mistakes.

So, yesterday I picked an interesting ticket and the process I had to do is investigation, making change in the code, deploy, test and done. But, this process got pressured by my stupid thoughts about my own knowledge and capabilities, there was some hiccups in internal systems pushing me out the zone of fixing a bug. The result is a crappy implementation and I made two rookie mistakes in the code. Luckily, the reviewer caught these issues, and we don't have tests.

The lesson is keep calm, if you don't feel you can keep it up take a short break to get back to the zone.

What were the things pushing me out from flow?

**Imposter syndrome**: after an hour long investigation which included checking database side, code side and so on I make my hypothesis to add additional logging. And this stupid imposter syndrome hit me. Am I sure this is the good solution? Am I allowed to add further logging? How others in the team will see me if I suggest this? All the stupid shit like this. I had to tell myself many times that this is the first version of the hypothesis and I need validation. Problem solving happens by taking steps on the theory - validation ladder.

**Imagined time pressure**. Due to my imposter syndrome I have a stupid expectation to do everything fast and knowing everything by heart. I basically don't give myself time to understand the problem and working on solutions. There is a voice in my mind punishing me if I don't know everything by heart.

**Hiccups in our internal systems**. So, I was stressed out by medium level by my imposter syndrome when I wanted to double check my theory. And our internal system started doing silly things in an implicit way. It did not show details I were looking for (I saw these 20 minutes earlier), it did not load pages and so on. Since, I was stressed out a bit these things pushed me further down in the stress rabbit hole.

When I reached out to the team running this app they requested further info and this simple fact doubled my mental load resulting in further stress.

**Coming back from holidays**. I was out for a few days and getting back to the domain of work takes time. However, my day started with certain meetings and I did not have my normal pace of getting back to work. My picking up the happenings of the last a few days was postponed giving me some level of frustration, the meetings were pointless waste of time, but I had to be there, increasing further my frustration.

So, all of these added up. Stress increases adrenalin in your blood. Adrenalin cause elevated heartbeat, narrowed thinking and vision. In this case it did not allow me to apply my wide thinking methods to solve problems. As I went down the stress rabbit hole the unsuccessful solutions, the frustrations and my imagined impossible expectations how I should do my job further increased the stress narrowing further my thinking.

**The result is a garbage MR.**

I should have stood up and give a break for myself to calm down. I have this skill, I am a veteran and I am absolutely aware of that if things will be done 5-10 minutes later in my job nobody will die, so I can take a break. Sometimes, it just doesn't work.