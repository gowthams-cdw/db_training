- Install mysql server
```
brew install mysql
```

- Start mysql services and setup services
```
brew services start mysql #start sql services
mysql_secure_installation #set up password
```

- Create custom user
```[sh]
mysql -u root -p                                             #log in as root user
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password'; #create a new user
GRANT ALL PRIVILEGES ON *.* TO 'silentFellow'@'localhost';   #grant all permissions
FLUSH PRIVILEGES;                                            #apply changes
EXIT;                                                        #exit and relogin as new user
```

- Install mysql workbench
```
brew install --cask mysqlworkbench
```

- Setup workbench with
    - `Host`: localhost
    - `Port`: 3306
    - `User`: created user
    - `Password`: created user password
