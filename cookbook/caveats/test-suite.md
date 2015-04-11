Running the Test Suite
======================

Running the test suite for elcodi/elcodi is simply one command:

```
php bin/phpunit
```

However in order to don't keep state on a temporal database you may need
to remove temporal files on your tmp folder. On mac you can just do:

```
rm -rf /tmp/*.backup.database
```

Also running the test suite for elcodi/bamboo is also simple:

```
php bin/behat
```

However the first time you want to create a folder inside your /tmp:

```
mkdir -p /tmp/Bamboo
```

That way it will not complain it cannot create certain files inside.
