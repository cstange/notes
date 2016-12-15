
# CPU INFO

```bash
while true; do (echo $(date +"%T") $(top -bn1 | grep load | awk '{printf "CPU Load: %.2f\n", $(NF-2)}')  $(free -m | awk 'NR==2{printf "Memory Usage: %s %s MB (%.2f%%)\n", $3,$2,$3*100/$2 }') >> cpu.log   ); sleep 1; done &
tail -f cpu.log
```

# LOG CONTROL TO MAIL

```bash
#!/bin/bash
# cronlog.sh
# crontab -u user -e
# */10 8-20 * * 1,2,3,4,5 sh /home/user/cronlog.sh 2>&1

TIMESTAMP=`date "+%Y%m%d%H%M%S"`

cd /home/user

echo $TIMESTAMP > cronlog.log

touch olderr.log

echo 1 > lasterr.log

for i in $(find /home/user/log_* -mmin +30); do
        tail -n500 $i | grep -C 50 "SEVERE" >> lasterr.log
done;

diff -q lasterr.log  olderr.log && exit

cp -f lasterr.log olderr.log

#SEND MAIL lasterr.log
```


