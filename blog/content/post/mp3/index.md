+++
title = 'Machine Problem 3'
date = 2024-04-30T13:41:19+08:00
draft = false 
authors = ["Joe Mama"]
+++
---
# Web Application Vulnerabilities
## TL;DR
Attacks:
- SQL Injection (login, sesion token)
- Stored XSS attack
- CSRF Attack (form)

Fixes:
- Query Parameterization
- Sanitization (replace '<':'&lt', '>':'&gt')
- CSRF Token
- Rate Limiting (disallow brute force)

[click me for repo link to patched-app](https://github.com/sycasec/mp3-vuln-app)

## Attacks
### Just the tip (basic login SQL injection)
After starting up the service, we are greeted with a simple login page.
One look at this login page and you can tell that this website’s TOTALLY *asking for it*.
Look at that sweet login form. It needs to be broken in with some *Big Bad Code* (injection). So lets put **just the tip** in first:
```SQL
'OR 1=1--
```

Recalling our `SQL Injection` lecture that for vulnerable websites that naively fetch a single user using direct-input query, we can force a login by making the query return *at least one* entry.

{{< figure src="init.png" alt="starting point" >}}

We click login:

{{< figure src="login_success.png" alt="sql injected" >}}

Now that we're here, lets do a little bit of trolling.

### XSS Attack!
We can try a nifty little exploit to test if our inputs are being sanitized:

```html
<script>alert("XSS attack!")</script>
```

We hit post, and:

{{< figure src="stored_xss.png" alt="starting point" >}}

We can also do something a little more evil, like

```html
<img src="/logout"/>
```

which effectively sends a valid `GET` request to the logout endpoint. After clicking post:

{{< figure src="xss_logout.png" alt="logout request" >}}

Immediately getting kicked out of the site is kind of annoying though, and we dont really have any way of deleting posts, so lets just wipe those from the database directly.

### CSRF Attack!
We also can do a little bit of trolling from the outside, like a CSRF attack, like so:

```html
<html>
  <head>
    Sample CSRF Attack
  </head>
  <body>
    <form
      id="evilform"
      name="evilform"
      action="http://127.0.0.1:5000/posts"
      method="POST"
      target="popup"
    >
      <input type="hidden" name="message" value="CSRF Attack!" />
      <button type="button" onclick="submitForm()">Launch Attack</button>
    </form>

    <script>
      function submitForm() {
        const newWindow = window.open("", "popup", "width=200,height=200");
        document.getElementById("evilform").submit();
      }
    </script>
  </body>
</html>
```

which looks like this:
{{< figure src="CSRF_html.png" alt="sample csrf" >}}

Executing the attack:

{{< figure src="csrf_attack.gif" alt="hell yeah" >}}

### Session Token SQL Injection

Since there was not much else we could do, taking a look at the code reveals a vulnerable session token query - which we can exploit. First, we must make sure that the session token in the database is not deleted, which means we cannot send a `GET` request to the `logout` endpoint.
What we do is delete the `session_token` cookie stored in our browser and reload the site - this way we get logged out without having the `session_token` stored in the database deleted.

In a realistic scenario, we can just assume that we are trying to login to an account of a user that's current logged in. Anyway, lets do that attack now - similar to our basic `SQL injection` payload, we craft a `session_token` cookie in our browser:

{{< figure src="session_token_inject.png" alt="session token inject" >}}

We reload this to send the cookie, and just like that:

{{< figure src="session_token_login.png" alt="session token login" >}}

## Fixes
### ????????????
Literally `?` or parameterization saves us from `SQL` injection, like so:

```python
# parameterize session tokens
res = cur.execute("SELECT username FROM users INNER JOIN sessions ON "
                  + "users.id = sessions.user WHERE sessions.token = ?",
                  [request.cookies.get("session_token")])
...
# parameterized username and password
res = cur.execute("SELECT id from users WHERE username = ? AND password = ?", [request.form["username"], request.form["password"]])
```

We apply this to every SQL query made. The fully patched code is located [here](https://github.com/sycasec/mp3-vuln-app)

### Bleach
Literally just swap out croccy symbols. We use a helper function `sanitize` like so:

```python
def sanitize(message: str):
    return message.translate(str.maketrans({'<':"&lt", '>':"&gt"}))

#.... /posts route code goes here ....
if user:
    cur.execute("INSERT INTO posts (message, user) VALUES (?,?);",
                [sanitize(request.form["message"]), str(user[0])])
    con.commit()
```
<!-- TODO: Add a picture showing this! -->

### Not today sir, CSRF only
We can simply implement a CSRF Token to disable these kinds of attacks like so:
```python
# /home route code goes here....
user = res.fetchone()
if user:
    # Generate CSRF Token
    csrf_token = secrets.token_urlsafe(16)

    res = cur.execute("SELECT message FROM posts WHERE user ='" + str(user[0]) + "';")
    posts = res.fetchall()

    resp = make_response(render_template("home.html", username=user[1], posts=posts, csrf_token=csrf_token))
    resp.set_cookie("csrf_token", csrf_token, httponly=True)
    return resp
```

Following the advise of our wise elders, we generate a CSRF token everytime our user fetches a form, and we send it as a cookie to them and also place it as a hidden input in our form code:

```html
<h2>Welcome, {{username}}!</h2>
      <a href="/logout">Logout</a>
  <h3>Posts</h3>
  <form method="post" action="/posts">
    <input type="text" name="message" />
    <input type="hidden" name="csrf_token" value="{{ csrf_token }}" />
    <input type="submit" value="Post!" />
  </form>
```

and of course we check for the CSRF token when a `POST` request is made to the `/posts` route:

```python
@app.route("/posts", methods=["POST"])
def posts():
    cur = con.cursor()
    if request.cookies.get("session_token") and 
        request.form.get("csrf_token") == request.cookies.get("csrf_token"):
    # the rest of the /post route code goes here...
```

We didn't really change much of the code in the app after this, so a valid POST request is made resulting in a `302 FOUND` response, but it doesn't get past the `csrf_token` check so nothing is done beyond that.

<!-- TODO: Add a picture showing this! -->

## What else can we do
Alright, we fixed the major security flaws. But to make this MP more ~~complicated~~ interesting, we can also implement a simple rate limiter to prevent brute force attacks. 

For the sake of not rewheeling the invention, we can just install the Flask Limiter extension. The simplest setup (yoinked from the [documentation](https://flask-limiter.readthedocs.io/en/stable/)) is enough for this MP. 

We setup the limiter like this 

```python
limiter = Limiter(
    get_remote_address,
    app=app,
    storage_uri="memory://",
)
```

We can now limit the access to our endpoints.

For the `login` endpoint, we can use multiple rate limits for funsies.

```python
@limiter.limit("1/second")
@limiter.limit("10/hour")
@limiter.limit("100/day")
def login():
    ...
```

For the `posts` endpoint, we can do something like this
```python
@limiter.limit("2/second")
def posts():
    ...
```

When a user reaches the rate limit, they will get these errors and won’t be able to make requests until some time has passed.

{{< figure src="MP3 writeup-20240501070954452.webp" alt="rate limit error" >}}

{{< figure src="MP3 writeup-20240501071021422.webp" alt="rate limit error" >}}

{{< figure src="MP3 writeup-20240501071136899.webp" alt="rate limit error" >}}

To make this even more interesting, we can check if this actually works using other devices. Unfortunately, using `flask run` won’t allow other devices to connect to our server, even if we expose port 5000 in our Firewall. Although this is [not recommended](https://flask.palletsprojects.com/en/3.0.x/quickstart/#public-server), we can just simply run flask with `flask run --host=0.0.0.0` while still exposing port 5000 in our Firewall. 

I'm using my phone to test this.

{{< figure src="MP3 writeup-20240501071434864.webp" alt="rate limit error" >}}

## Emploice Muswashans

{{< figure src="emploice.png" alt="emploice" >}}

So what have we learned? 

We learned to clean our inputs. And, well, use CSRF tokens. 

Most of the fixes are just sanitization with extra steps.

Don't be like 2014 TweetDeck that forgot to do this. 

{{< youtube zv0kZKC6GAM>}}

Or that one University portal that still doesn't do this (yes, we're all looking at you). 

Or that other University that just got their employees' salaries leaked.

{{< figure src="ctu_leaked.png" alt="CTU leak" >}}


