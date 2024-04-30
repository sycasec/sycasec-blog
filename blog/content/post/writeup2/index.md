+++
title = 'Writeup2'
date = 2024-04-15T18:20:10+08:00
lastmod = 2024-04-15T22:07:20+08:00
draft = false 
authors = ["Alwyn Dy", "Kyle del Castillo"]
+++
---
# CAPTCHA 
*Does your website resemble a ghost town… for humans? Is your visitor counter mysteriously stuck on "0" despite having the most `fire` content on the web? Are you tired of bots feasting on your data like ravenous pigeons on a perfectly good croissant?*

*Then fear not, frustrated friend! Because now there's Completely Automated Public Turing test to tell Computers and Humans Apart, or what we, in the industry call CAPTCHAs – the revolutionary solution to your bot woes! Imagine a world where your servers sing sweet serenades of uptime, unburdened by the relentless clicker-clacker of bot traffic. A world where your content is only enjoyed by real, live, breathing eyeballs! (Well, mostly eyeballs.)*
*Don't settle for second-rate bot-blocking solutions. Embrace CAPTCHAs today! (Side effects may include temporary user frustration. But hey, at least it's not bots, right?)*

Joking aside, anyone who’s chronically online has definitely encountered this frustrating, annoying, persistent malevolence of a technology that sometimes you just can’t answer correctly. 

Remember when Captchas used to look like this? 

{{< externalImage src="http://geoffliu.files.wordpress.com/2010/04/recaptcha.png" alt="Old CAPTCHA" >}}
Pepperidge Farm remembers.

Anyway, we were way too young to even understand what exactly these things were trying to accomplish. To young little me (Kyle btw) (and probably to you too), they were just a fun little puzzle to solve before you could access the website, a bit annoying but nothing too serious - I was too excited to tend to my digital pet in Pet Society to care about the implications of these challenges. I never did pay these things much mind, until I encountered captchas like this:

{{< figure src="venticaptcha.png" alt="genshin CAPTCHA" >}}

And some more creative ones like these:

{{< figure src="order.jpg" alt="order CAPTCHA" >}}

{{< figure src="rotate_captcha.png" alt="rotate CAPTCHA" >}}

So I got really curious. What unspeakable evils are these things really stopping? Is it really worth all the effort from the developer’s side and the user’s side? Is Big CAPTCHA keeping a dark secret right under our noses?

## The D̴͇̯̋ě̵̹͙a̴̹̳̎d̶̡͉̑ ̷͍̄I̷̬̮̍n̵͈̣̔́t̵̜̩̋e̴̛͓ŗ̴̖̅ṅ̵̪͜ȅ̴̳ͅt̶̩̀ Theory

In Ye Olden Days of Friendster, MySpace, and shady message boards, the internet was *the* `wiki-wiki-wild-wild west`.
Bots roamed free, scraping data, spamming forums, and generally being a nuisance.
It's in pretty recent memory that engagement farms were a thing, somebody pays a "hacker" to boost a profile's likes and all of a sudden a lot of unknown people like a post, a profile picture, or a page.
It was a mess.
Granted, I no longer have screenshot of these things, but I obviously remember these existing. 
Local pageants that take to facebook for a community favorite with a single entry gaining almost about 40,000 likes and all of them unrelated to the page! It was absurd, and it was a problem. 


Conspiracy theorists would cook up all sorts of stories about how the internet was being taken over by bots, how the bots were going to take over the world, and how the bots were going to steal our data and sell it to the highest bidder. 
Other theories range from the NSA and CIA using bots to influence the way people think from online engagement, to the Chinese government using bots to influence public opinion on certain candidates.

This is also known as the *`Dead Internet Theory`*. Back when programming was an esoteric career path, nobody really understood how anything worked. The internet was a mysterious, uncharted territory, and all the cool kids hanged out in club penguin.

The Darknet aside, would somebody think about the identities stolen by bots? Millions of families suffer each year. We need a solution!

## 𝕮𝖆𝖕𝖙𝖈𝖍𝖆 𝕯𝖊𝖊𝖟
CAPTCHA, or Completely Automated Public Turing test to tell Computers and Humans Apart, is a system that prevent automated bots from accessing a website or its functionalities by forcing the user (or the bot) to answer one or more challenges. These challenges are designed to make it easy for humans to answer but difficult for bots, at least in theory. It was first developed in the early 2000s when the internet was at its infancy and bots were much simpler. Over the years, however, bots have gotten more sophisticated that they can now subvert a simple CAPTCHA of a distorted text image. Thus, CAPTCHAs evolved and new challenges were created to try and get ahead of the bots. From simple distorted text images, modern CAPTCHAs have evolved into various forms that either require the user to select a bunch of images that match the given description, slide a puzzle piece to complete the image, or to just click a button. Some newer (and questionable) CAPTCHAs even analyze the mouse movements and the cookies of the user—eliminating the need for direct interaction with the CAPTCHA.

The CAPTCHA system serves a very important role in the internet, not only it protects a website and its infrastructure, but also it ensures the privacy and security of its users. To a ticketing platform, CAPTCHAs block scalpers from buying the tickets and potentially reselling it at a much higher price. To a university portal, it reduces the likelihood of the servers being overwhelmed by bot traffic. To a social media platform, it prevents bots from scraping valuable information about its users and possibly using it for malicious purposes.

## Gotta Ⓒⓐⓟⓣⓒⓗⓐ  them all?

{{< figure src="sumisu.avif" alt="Agent Smith Scene" caption="Never send a human to do a machine's job.">}}

Despite its benefits, CAPTCHAs have also been infamous for its numerous issues and flaws that affect its usability, as well as the user’s security and privacy. Even its effectivity is debatable when considering existing methods to defeat it. On top of usability issues, methods for subverting CAPTCHAs, even the newer ones, have been evolving and improving. Advancements in AI models and computer vision has allowed bots to continuously break existing CAPTCHAs. There may even come a time where all CAPTCHA systems will be broken.

There are also services that passes the burden of answering these challenges to actual people. A quick Google search yields captcha2cash.org and 2captcha.com as the top 2 results, with the former paying $1 per 1000 captcha solves, an enticing offer especially to residents of third-world countries. 

Even the new CAPTCHA developed by Google that supposedly fixes the issue of usability by analyzing the behavior of the user (or bot) behind the scenes is riddled with security and privacy concerns. By running without the user’s knowledge, Google, a suspicious company to begin with, can collect whatever data they want under the guise of CAPTCHA (well, its not like they are not doing it already with how pervasive they are in the internet). 

Ironically, CAPTCHAs, which are primarily used to deter bots from accessing a website, are also being used to train AI models for Google Maps[^2] and in digitizing archival texts. Thus, by answering these CAPTCHAs, we become their unpaid oblivious annotators. This is yet another quirk of capitalism: *`create the problem, then sell the solution`*.

## JUST LEMME IN
CAPTCHAs have been known to degrade the overall user experience by breaking the flow of the user when accessing and navigating websites. Google even admitted that some CAPTCHAs are hard to solve. And, from observation, some are just plainly wrong. 

I’m sure you’ve experienced a few such CAPTCHAs where you were asked to select all squares with traffic lights and deliberate whether to include that one square that contains just the corner of the traffic light. Or perhaps you were prompted to select all images of bicycles and, after carefully and patiently doing exactly that, got an invalid answer.  Or maybe you were in the middle of researching something and was interrupted by a random CAPTCHA that caused you to lose your train of thought.

These scenarios are just few of the many issues that surround the process of answering CAPTCHAs. Sure, they are supposed to protect the websites, the infrastructure, and our data. But what about usability and user convenience? What about those with disabilities that cannot answer these challenges correctly?[^1] How should we balance the economics of security and utility?


## So, what now?

{{< figure src="https://media1.tenor.com/m/uBcbvnuP3K0AAAAd/lil-yachty-drake.gif" alt="fix bug plz" caption="Can we fix it?">}}


From the discussion above, the CAPTCHA system is riddled with unanswered questions and unaddressed problems. Although the internet still stands today even with this flawed system, we should probably address these issues. Or should we?

Well, as someone (aka Alwyn) who has developed a simple Facebook post scraper within a week, I understand the capabilities of bots and the need to have something that protects us from these malicious programs. Even a rudimentary bot like the one I created can access thousands of pages and fill out and submit hundreds of forms in a matter of minutes. How much more can a more advance bot do!

Okay. Okay. But how? What should the new and improved system look like? How should it work? Who should lead in its creation?

Answering all these questions and coming up with a robust system is beyond my abilities as an undergraduate student. However, by considering the questions and problems that was discussed and looking at how the other security measures overcame their challenges, as well as the security principles, we may perhaps get glimpse of what this ideal system would look like. 

## The Future of CAPTCHA
First, no company should have the monopoly or sole ownership of this new CAPTCHA. Preferably, it should be open source to ensure that the data collected, if any, is documented and public knowledge. It is interesting that data collected in the current CAPTCHAs are used to digitize archival texts. Although this is not necessarily negative, this use of the collected data should be publicized.

Second, it should have the least amount of friction and should cater to all kinds of disabilities. Usability is important in the internet and highly prized by the users[^3], thus answering CAPTCHAs shouldn’t be much of a detraction from what the user was previously doing. Current CAPTCHAs, especially on mobile phones, have shown that these challenges can be fun and creative. CAPTCHAs should also be integrated well with existing assistive technology and offer different forms of puzzles that the user can choose whichever they are comfortable answering.

Third, it should follow Kerckhoff’s principle. Similar to how one-way functions work, where even if the algorithm is public knowledge, the CAPTCHA system should also remain secure even if it becomes open source. As a security system, this aspect is crucial.

Fourth, following the defense in depth principle, it should be used together with other security measures. No matter how robust this new CAPTCHA would be, we should assume that it would fail. Thus, layering defenses is important to ensure that our system remains secure when the CAPTCHA is subverted.

# References
- Burgess, M. (2017, October 26). _Captcha is dying. This is how it’s being reinvented for the AI age_. WIRED; WIRED. https://www.wired.com/story/captcha-automation-broken-history-fix/
- Cloudflare. (2024). _How CAPTCHAs work | What does CAPTCHA mean?_ Cloudflare.com. https://www.cloudflare.com/learning/bots/how-captchas-work/
- Waters, S. (2021, May 19). _Ho place.w to Solve Captchas—and Why They’ve Gotten So Hard_. WIRED; WIRED. https://www.wired.com/story/im-not-a-robot-why-captchas-hard-to-solve/

‌
[^1]: Can Hawking even pass these challenges? 
[^2]: A funny anecdote: when I noticed this years ago, I purposefully answered the second challenge incorrectly to introduce noise in their training data >:)
[^3]: This is the reason why people are so apprehensive with YouTube’s new ads policy
