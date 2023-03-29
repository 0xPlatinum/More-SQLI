# More-SQLI
More SQLI was a PicoCTF challenge

# Challenge

![image](https://user-images.githubusercontent.com/98354876/228391879-780d031a-84b4-404c-bb96-d72a8362fc1a.png)

# Initial Reconnaissance 

![image](https://user-images.githubusercontent.com/98354876/228391847-4b91220f-68bc-41b7-93c0-e943a4304e3e.png)

We are greeted with a classic login screen, telling us this is a Security Challenge and to login! 

Well I mean, the title *is* more SQLI, why not try some SQLI?

![image](https://user-images.githubusercontent.com/98354876/228392066-08890fd7-1d0b-4eb0-a4a4-a4bce2d6a1d1.png)

This is a commonly used SQLI payload, so let me break it down.

What we are doing is injecting into an SQL query (or attempting to)!

Entering a input like "admin" "password" in the username and password fields respectively results in this

![image](https://user-images.githubusercontent.com/98354876/228392874-8d5e4462-09cb-4b57-ac4c-b85c50d88deb.png)

This is vulnerable to SQLI, it is directly putting our username input and password input into the SQL query, meaning we can control the query to an extent!
But.. How? Well thats where the SQLI payload from above comes in handy!

Notice how there are single quotes around our user input in the screenshot above? Well if our input is being directly put in there.. we need to escape those single quotes. Thats where our first part of our SQLI payload comes from, we put a single quote to escape the query. Now we need to make sure that the "password" parameter results in true, even if its never matching a valid password! Thats where the "or 1=1" part of the payload comes in. We are basically saying to the server "Ok, find me a password that is empty (Due to us closing the single quote) OR does 1=1?" And because theres no password that is blank, it checks our OR statement and sees that 1 does in fact equal 1! So it sets the password check to TRUE. But remember, we need to 
