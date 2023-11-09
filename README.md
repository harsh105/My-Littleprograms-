
jps -l | awk '{print $1}' | xargs -I % sh -c 'echo -n %: ; netstat -tuln 2>/dev/null | awk /\:%/' | awk '{print $1, $NF}'

≠=============

#!/bin/bash

# Name or part of the name of the Java application
APP_NAME="your-app-name"

# Check if the application is running
PID=$(jps | grep "$APP_NAME" | awk '{print $1}')

if [ -n "$PID" ]; then
    echo "The application is running with PID: $PID"
else
    echo "The application is not running."
fi

=============
#!/bin/bash

# Specify the PID of your Java application
PID="your-java-app-pid"

# Use netstat to fetch the port number
PORT=$(netstat -ano | grep "LISTENING" | grep "$PID" | awk '{print $4}' | awk -F: '{print $NF}')

if [ -n "$PORT" ]; then
    echo "Port number of the Java application with PID $PID is $PORT"
else
    echo "Port number not found for PID $PID"
fi
