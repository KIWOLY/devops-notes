Logs Commands
In DevOps you read logs every single day — when something breaks, logs tell you exactly what happened.

1. tail — most used log command
bash# Follow live logs in real time (most used)
tail -f /var/log/syslog

# Show last 50 lines
tail -n 50 /var/log/syslog

# Follow multiple log files at once
tail -f /var/log/syslog /var/log/auth.log

# Follow AND show last 100 lines
tail -fn 100 /var/log/nginx/access.log




6. Important log file locations
/var/log/syslog          # general system logs
/var/log/auth.log        # login and SSH attempts
/var/log/kern.log        # kernel messages
/var/log/dpkg.log        # package install logs
/var/log/nginx/          # nginx web server logs
/var/log/nginx/access.log   # every request to nginx
/var/log/nginx/error.log    # nginx errors
/var/log/apache2/        # apache web server logs
/var/log/mysql/          # mysql database logs
/var/log/docker/         # docker logs


7. Docker logs
bash# View logs of a container
docker logs container_name

# Follow live
docker logs -f container_name

# Last 100 lines
docker logs --tail 100 container_name

# Show timestamps
docker logs -t container_name

# Combine follow and last 50 lines
docker logs -fn 50 container_name




curl — Transfer Data from URLs
curl is your Swiss army knife for making HTTP requests from the terminal. Used constantly in DevOps.

Basic usage
# Simple GET request
curl https://google.com

# Get your public IP
curl ifconfig.me

# Get just the response (silent mode)
curl -s https://api.example.com




1. GET request
bash# Basic GET
curl https://api.example.com/users

# With headers shown
curl -i https://api.example.com/users

# Show only headers
curl -I https://api.example.com

# Follow redirects automatically
curl -L https://example.com

# Silent — no progress bar
curl -s https://api.example.com



2. POST request
bash# POST with JSON data
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name": "kiwoly", "role": "devops"}'

# POST with form data
curl -X POST https://example.com/login \
  -d "username=kiwoly&password=mypass"



curl vs wget

            curl                                    wget
Main     useAPI calls, testing                  downloading file