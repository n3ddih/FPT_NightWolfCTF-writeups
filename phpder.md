# PHPDER Writeup

At first glance it is a login page

![image](https://user-images.githubusercontent.com/80664686/117576250-66aa6180-b10f-11eb-8036-3bdadc9e0a61.png)

I tried a bit of SQL injection but there's just some *Invalid Login* error

Ran `gobuster` to do some recon and found `/backup.zip` file. Unzip it and there're the sources of the web

File: [backup.zip](files/backup.zip)
