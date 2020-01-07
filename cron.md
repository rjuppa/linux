### crontab
```
https://crontab.guru

crontab -e   (edit)
crontab -l   (list)
crontab -r   (remove)

10 * * * * /usr/bin/php /www/virtual/username/cron.php > /dev/null 2>&1   # discard output
10 * * * * /usr/bin/php /www/virtual/username/cron.php > /var/log/cron.log


0 = Sunday
1 = Monday
2 = Tuesday
3 = Wednesday
4 = Thursday
5 = Friday
6 = Saturday

# Use the hash sign to prefix a comment
# +---------------- minute (0 - 59)
# |  +------------- hour (0 - 23)
# |  |  +---------- day of month (1 - 31)
# |  |  |  +------- month (1 - 12)
# |  |  |  |  +---- day of week (0 - 7) (Sunday=0 or 7)
# |  |  |  |  |
# *  *  *  *  *  command to be executed

kazdych 10 minut v pracovni dobe od 9-17
10 9-17 * * 1-5

kazdou nedeli ve 22:30
30 22 * * 0

v 6:30 rano jednou za dva dny
30 6 * * */2

prvni pondeli v mesici
30 8 1-7 * 1

v 12:05, 12:20, 12:35, 12:50 kazdy den
```
