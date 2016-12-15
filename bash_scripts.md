
# CPU INFO

while true; do (echo $(date +"%T") $(top -bn1 | grep load | awk '{printf "CPU Load: %.2f\n", $(NF-2)}')  $(free -m | awk 'NR==2{printf "Memory Usage: %s %s MB (%.2f%%)\n", $3,$2,$3*100/$2 }') >> cpu.log   ); sleep 1; done &
tail -f cpu.log

# LOG CONTROL






