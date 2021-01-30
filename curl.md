## curl w/ auth, -L to follow redirects
curl http://admin:pass@website.com/ 
curl -u admin:pass http://website.com
curl -u admin:password 'http://inlanefreight.com/search.php?port_code=us'

# POST
curl -d 'username=admin&password=password' -L http://inlanefreight.com/login.php

# DEBUG it
curl -d 'username=admin&password=password' -L  http://inlanefreight.com/login.php -v
#Cookies
curl -d 'username=admin&password=password' -L --cookie-jar /dev/null  http://inlanefreight.com/login.php -v
curl -d 'username=admin&password=password' -L --cookie-jar cookies.txt  http://inlanefreight.com/login.php

curl http://inlanefreight.com/admin/dashboard.php -v
curl --cookie cookies.txt http://inlanefreight.com/admin/dashboard.php -v

# Content-type header
curl -H 'Content-Type: application/json' -d '{ "username" : "admin", "password" : "password" }' --cookie-jar /dev/null -L  http://inlanefreight.com/login.php

## PUT/DELETE 
curl -X OPTIONS http://inlanefreight.com/ -vv
# FILE UPLOAD
echo "curl file upload!" > test.txt
curl -X PUT -d @test.txt http://inlanefreight.com/test.txt -vv
curl http://inlanefreight.com/test.txt

curl -X DELETE http://inlanefreight.com/test.txt -vv
