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
# Exploitation
This is vulnerable to SQLI, it is directly putting our username input and password input into the SQL query, meaning we can control the query to an extent!
But.. How? Well thats where the SQLI payload from above comes in handy!

Notice how there are single quotes around our user input in the screenshot above? Well if our input is being directly put in there.. we need to escape those single quotes. 

Thats where our first part of our SQLI payload comes from, we put a single quote to escape the query. Now we need to make sure that the "password" parameter results in true, even if its never matching a valid password! 
Thats where the "or 1=1" part of the payload comes in. We are basically saying to the server "Ok, find me a password that is empty (Due to us closing the single quote) OR does 1=1?" 


And because theres no password that is blank, it checks our OR statement and sees that 1 does in fact equal 1! So it sets the password check to TRUE. But remember, we need to make the username check true as well
Or.. We can just ignore it! Thats what the final part of the query is doing, the characters "-- -" is actually a comment in the SQL language! If you check the query above, we can see the password check comes before the username check.


This means we can enter 'or 1=1 to bypass the password check, and add "-- -" to comment out the username check so its never ran in the first place! This is especially useful for when there is a part of the query we cant control, that check would likely fail, but if we can comment out the rest of the query, it doesnt matter whats after our user controlled input!

Now.. Lets run this payload!

![image](https://user-images.githubusercontent.com/98354876/228394156-f31bda53-2bd1-4dc2-a588-356cad9e6fef.png)

It worked! Aha! We are masters of SQLI! Now.. Wheres that sneaky flag..?

It looks like we have some database of users, their addresses, and their phone numbers. Well assuming this is using SQLite3, we can try to do some wild card injections. Wild cards are things that match in certain ways. For example, the "_" tells the SQL query to match *any* one letter!

So if we did a query for ma__, it would match mall, mail, mate, mame. 
Then we have the all powerful "%". This matches.. everything. No like it matches every single thing in the database. you *can* do things like %end and it matches stuff with "end" at the end.
Lets try that!

![image](https://user-images.githubusercontent.com/98354876/228394728-b67a449a-8a47-4181-8be0-97321b57dd41.png)

Nothing seems to have changed?

# Confusion

Thats right, its time for the small stump I was stuck on during this challenge.
I did a bunch of different things, like trying to leak the database Tables, Columns, and Rows to find the flag in a potentially hidden column table or row. Alas, no results.
So, I took a breather and decided to go through the steps one at a time.
What if its in the source code of the website? Devs sometimes do that, lets take a look.

![image](https://user-images.githubusercontent.com/98354876/228394924-6a5e8af8-e2e2-4655-8905-872566fbc740.png)

Alas, nothing there.
With no ideas left, I decided to send my SQLI request to Burpsuite (A proxy that lets me edit my requests!)

Lets log out and login again with the SQLI payload, but this time send the request to burpsuite.

![image](https://user-images.githubusercontent.com/98354876/228395095-6aabdf77-c529-4d7c-92a4-fb9cc38b58cf.png)

This request *looks* normal, atleast no weird headers or cookies. Lets send the request to Burpsuites Repeater, a service that lets me send requests repeatedly! 

![image](https://user-images.githubusercontent.com/98354876/228395757-bfe3b361-57ec-401b-a597-5cdc5a172dfd.png)

And what do you know! The server was actually making a 302, which is a redirect! Once we sucessfully passed the SQLI, it gave us the flag and quickly redirected away to welcome.php!
Thus we have obtained our flag

- picoCTF{G3tting_5QL_1nJ3c7I0N_l1k3_y0u_sh0ulD_c8b7cc2a}
