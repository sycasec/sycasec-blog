+++
title = 'Machine Problem 2'
date = 2024-04-30T13:41:19+08:00
draft = false 
+++
---
# You are blocked!!
To make this MP more ~~complicated~~ interesting, we can add a simple rate limiter on the number of login attempts a user can do. This increases the security of our web app by preventing attackers from brute forcing the login page.

But first, we need to set up our database to store the login attempts. We create a new table, `login_attempts`, by running the SQL below on our `app.db` file.

```SQL
CREATE TABLE login_attempts (
	id INTEGER PRIMARY KEY AUTOINCREMENT,
	remote_address TEXT,
	timestamp datetime
);
```

The `id` column is set to `AUTOINCREMENT` to be consistent with the other tables. The `AUTOINCREMENT` keyword allows SQLite3 to keep track of the last id used in the table using the internal table`sqlite_sequence`. The `timestamp` column stores the timestamp of the recent login attempt, regardless of whether it was successful or not. And the `remote_address` column stores the IP address of the remote client that is attempting to login and can be retrieved from `request.remote_addr`.

Every time a user attempts to login: 
- a row is inserted into the `login_attempts` table containing their IP address and its corresponding timestamp.
- The function `is_rate_limited` is then executed that checks whether the remote client has attempted one too many login attempts.

If the client has indeed exceeded the allowable attempts in a given time frame, the user is prevented from another login attempt and an error `Login attempts exceeded limit.` is shown. Otherwise, the user can try for another attempt.

The function `is_rate_limited` executes an SQL query that retrieves the number of attempts from a remote client in a given time window which is set at 5 minutes. If the number of attempts exceed the maximum allowable attempts, the remote client is rate limited.

<!-- ![[MP3 writeup-20240430202620147.webp]] -->
{{< figure src="MP3 writeup-20240430202620147.webp" alt="rate limit error" >}}

<!-- ![[MP3 writeup-20240430203445363.webp]] -->
{{< figure src="MP3 writeup-20240430203445363.webp" alt="rate limit error" >}}

To determine whether this security measure is actually working, we need to test it with other devices. Unfortunately, using `flask run` won’t allow other devices to connect to our server, even if we expose port 5000 in our Firewall. Although this is [not recommended](https://flask.palletsprojects.com/en/3.0.x/quickstart/#public-server), we can just simply run flask with `flask run --host=0.0.0.0` while still exposing port 5000 in our Firewall. With this, we can now test it with other devices.

<!-- ![[MP3 writeup-20240430203817616.webp]] -->
{{< figure src="MP3 writeup-20240430203817616.webp" alt="rate limit error" >}}

<!-- ![[MP3 writeup-20240430203855760.webp]] -->
{{< figure src="MP3 writeup-20240430203855760.webp" alt="rate limit error" >}}

# Conclusion
*edit or add lang sad diri*
Notice that much of the exploits stem from not sanitizing user inputs. Although this is Web Security 101, popular platforms can still fail to implement this. For example, the infamous self-retweeting tweet in 2014 that exploited this vulnerability of TweetDeck using an XSS attack. 

![](https://www.youtube.com/watch?v=zv0kZKC6GAM)

This cautionary tale teaches us that, although these attacks are almost as old as the internet and the defenses against them have been an industry-standard for a long time, we still need to keep in mind that these vulnerabilities can be introduced into our applications, whether by sheer incompetence or malevolence. Thus, testing the systems we build for these vulnerabilities is an essential and unskippable step in ensuring its reliability and security,
