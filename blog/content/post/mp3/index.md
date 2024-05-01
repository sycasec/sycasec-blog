+++
title = 'Machine Problem 2'
date = 2024-04-30T13:41:19+08:00
draft = false 
+++
---
# You are blocked!!
To make this MP more ~~complicated~~ interesting, we can add a rate limiter on the number of login attempts a user can do. This increases the security of our web app by preventing attackers from brute forcing the login page.

For the sake of not reinventing the wheel, we will use the Flask Limiter extension. The simplest setup (yoinked from the [documentation](https://flask-limiter.readthedocs.io/en/stable/)) is enough for this MP. The limits `1/second`, `10/hour`,  and `100/day` are used in the login page to demonstrate the defense-in-depth principle. We will also add a `2/second` rate limiter to the `posts` API for funsies.

When a user (potentially an attacker) exceeds this limit, they will get the following errors and won’t be able to make requests until some time has passed.

{{< figure src="MP3 writeup-20240501070954452.webp" alt="rate limit error" >}}

{{< figure src="MP3 writeup-20240501071021422.webp" alt="rate limit error" >}}

{{< figure src="MP3 writeup-20240501071136899.webp" alt="rate limit error" >}}

To determine whether this security measure is actually working, we need to test it with other devices. Unfortunately, using `flask run` won’t allow other devices to connect to our server, even if we expose port 5000 in our Firewall. Although this is [not recommended](https://flask.palletsprojects.com/en/3.0.x/quickstart/#public-server), we can just simply run flask with `flask run --host=0.0.0.0` while still exposing port 5000 in our Firewall. With this, we can now test it with other devices.

{{< figure src="MP3 writeup-20240501071434864.webp" alt="rate limit error" >}}

# Conclusion
*edit or add lang sad diri*
Notice that much of the exploits stem from not sanitizing user inputs. Although this is Web Security 101, popular platforms can still fail to implement this. For example, the infamous self-retweeting tweet in 2014 that exploited this vulnerability of TweetDeck using an XSS attack. 

![](https://www.youtube.com/watch?v=zv0kZKC6GAM)

This cautionary tale teaches us that, although these attacks are almost as old as the internet and the defenses against them have been an industry-standard for a long time, we still need to keep in mind that these vulnerabilities can be introduced into our applications, whether by sheer incompetence or malevolence. Thus, testing the systems we build for these vulnerabilities is an essential and unskippable step in ensuring its reliability and security,
