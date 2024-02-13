+++
title = 'Writeup0'
date = 2024-02-09T23:17:32+08:00
draft = false 
background_image = "/images/hackerman.jpg"
+++

{{< youtube Qe73tRTksf0 >}}

Any decent (or old) cybersecurity expert knows about Kevin Mitnick. Famous for many of his early exploits, Mitnick is a black-hat turned white-hat hacker. Born in 1963, Mitnick’s first hack was at age 12, convincing a bus driver to tell him where he could get his own ticket puncher for “a school project”. He then looked for blank LA bus transfer tickets in dumpsters, using the punch machine to get unlimited, free rides.   

At age 16, Mitnick was dared by a hacker group to hack into a computer system named “The Ark” at Digital Equipment Corporation (DEC), a major American company in the computer industry from the 1960s to the 1990s. “The Ark” was used to develop and maintain the source code for DEC’s RSTS/E operating system. Mitnick posed as Anton Chernoff, one of the lead developers of “The Ark” at DEC and called the system manager claiming to have forgotten the password to one of the accounts. The system manager was convinced that Mitnick was the real deal, giving Mitnick access and allowing him to create a new password, and was given the precautionary dial-up password “buffoon”. In less than five minutes, Mitnick gained access to DEC’s RSTS/E OS with the privileges of a system developer. Mitnick demonstrated his access to his new hacker “friends”, and on the same day those “friends” went to another location, downloaded source-code components of the RSTS/E, and called DEC’s corporate security informing them of the hack and turning Mitnick in.


Mitnick spent most of his life working as a private investigator and social engineer. Mitnick was able to access and download copies of software from major tech companies like Sun Microsystems, Nokia, Fujitsu, Motorola, NEC, among others. Eventually, Mitnick was captured by the FBI along with John Markoff and Tsutomu Shimomura, and was sentenced to jail for 4 ½ years and 8 months in solitary confinement for allegedly $300 million, without bail and without trial.  

While Mitnick’s imprisonment was muddled with controversies, what remains true to this day is that Kevin Mitnick’s feats gave rise to the term “Social Engineering” - exploiting the humans in positions of authority to gain access to computer systems. ‌‌

## Humans as the weakest link in cyber security

{{< spotify type="episode" id="38RuwrVwAHNXgHLDOANtmj" width="100%" height="250" >}}

Many famous hacks involve exploiting a security weakness, like the NSO Group’s “Pegasus” spyware in 2016, which exploited several iOS zero-days, implanting rootkits from NSO servers into an iPhone and successfully jailbreaking it. While it could be argued that the Pegasus spyware exploited a human (in this case, an iOS dev’s) skill issue, humans being the weakest link in cyber security is best exemplified in Social Engineering attacks. 

Mitnick’s specialty was social engineering his way into computer systems. In the video below this paragraph, Mitnick details how he used deception to steal Motorola’s source code for the MiroTAC Ultra Lite Phone. 

{{< youtube fT8Jw-63MXM >}}

In the history of hacking, exploiting human error has always been at the core of hacking - no matter the technology involved. In the 1960’s, one of the first popularized hacks was telephone phreaking, where a “hacker” uses a high pitch noises to trick a phone into receiving operational commands, allowing a hacker to switch calls from the phone handset, allowing free, long-distance calls, along with allowing the hacker to manipulate the call’s geographical location and number.

This allowed hackers like Kevin Mitnick, among others, to gain access and privileges to systems they normally would not be able to, like how Mitnick detailed in the video above.

Another hack that showcases exploiting the human is phishing attacks, where the hacker sends a malicious email posing as a legitimate email - often asking a user to click link that downloads spyware.

{{< figure src="phishing.png" title="phishing.png" width="300" class="centered" >}}

Phishing attacks have become a staple for criminal activity, and are still found to be effective to this day, targeting corporate employees to gain access into otherwise secure systems. Many of the early internet viruses, ransomware, and spyware were propagated via Phishing, such as the [koobface](https://www.kaspersky.com/resource-center/definitions/what-is-the-koobface-virus) malware, the famed [ILOVEYOU](https://www.wired.com/story/the-20-year-hunt-for-the-man-behind-the-love-bug-virus/) virus by our very own Onel de Guzman, among many others. 

## mitigation techniques
> “i can only show you the door, you’re the one that has to walk through it” - morpheus (the matrix)


most cybersecurity breaches involve easily preventable exploits. the problem is that most 
corporations do not really factor security into their “agile” processes from the get go. 

{{< figure src="budget.png" title="budget.png" width="300" class="centered" >}}


any cybersecurity expert would recommend that training their employees in security is the most important security spending there is. designing with security in mind should be out of the question. however it is possible to overplan and overspend in security - hackers are persistent and can wait years just for one miracle loophole. security is a balance, and knowledge is power. just train the people in cybersecurity, dammit.

anyway, enjoy this video about sony triggering the anonymous group, and how chaotic an “anonymous” hacker group can be.


{{< youtube 66A4zcJaPLk >}}

## summary
> “choice. the problem is choice.” - neo (the matrix reloaded)

while the matrix quotes are cringe, i just couldn’t help it. the problem with most (if not all) software weaknesses are just humans. i mean, social engineering, phishing, and all hacks aside, who made the software that was hacked? who did the hacking? cyber security is an endless cat and mouse game, and all you can do is choose between the **red** team, and the **blue** team (haha see what I did there).

### sources
https://cyberexperts.com/kevin-mitnick-the-most-infamous-hacker-of-all-time/

https://passwordresearch.com/stories/story47.html

https://www.theregister.com/2003/01/13/chapter_one_kevin_mitnicks_story/

https://www.wired.com/story/the-20-year-hunt-for-the-man-behind-the-love-bug-virus/

https://www.kaspersky.com/resource-center/definitions/what-is-the-koobface-virus

