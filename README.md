
jps -l | awk '{print $1}' | xargs -I % sh -c 'echo -n %: ; netstat -tuln 2>/dev/null | awk /\:%/' | awk '{print $1, $NF}'
