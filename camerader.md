Examples

    Running cameradar on your own machine to scan for default ports

docker run --net=host -t ullaakut/cameradar -t localhost

    Running cameradar with an input file, logs enabled on port 8554

docker run -v /tmp:/tmp --net=host -t ullaakut/cameradar -t /tmp/test.txt -p 8554

    Running cameradar on a subnetwork with custom dictionaries, on ports 554, 5554 and 8554

docker run -v /tmp:/tmp --net=host -t ullaakut/cameradar -t 192.168.0.0/24 --custom-credentials="/tmp/dictionaries/credentials.json" --custom-routes="/tmp/dictionaries/routes" -p 554,5554,8554


Configuration

The RTSP port used for most cameras is 554, so you should probably specify 554 as one of the ports you scan. Not specifying any ports to the cameradar application will scan the 554, 5554 and 8554 ports.

docker run -t --net=host ullaakut/cameradar -p "18554,19000-19010" -t localhost will scan the ports 18554, and the range of ports between 19000 and 19010 on localhost.

You can use your own files for the credentials and routes dictionaries used to attack the cameras, but the Cameradar repository already gives you a good base that works with most cameras, in the /dictionaries folder.

docker run -t -v /my/folder/with/dictionaries:/tmp/dictionaries \
           ullaakut/cameradar \
           -r "/tmp/dictionaries/my_routes" \
           -c "/tmp/dictionaries/my_credentials.json" \
           -t 172.19.124.0/24

This will put the contents of your folder containing dictionaries in the docker image and will use it for the dictionary attack instead of the default dictionaries provided in the cameradar repo.
Check camera access

If you have VLC Media Player, you should be able to use the GUI or the command-line to connect to the RTSP stream using this format: rtsp://username:password@address:port/route
Command-line options

    "-t, --targets": Set target. Required. Target can be a file (see instructions on how to format the file), an IP, an IP range, a subnetwork, or a combination of those. Example: --targets="192.168.1.72,192.168.1.74"
    "-p, --ports": (Default: 554,5554,8554) Set custom ports.
    "-s, --scan-speed": (Default: 4) Set custom nmap discovery presets to improve speed or accuracy. It's recommended to lower it if you are attempting to scan an unstable and slow network, or to increase it if on a very performant and reliable network. You might also want to keep it low to keep your discovery stealthy. See this for more info on the nmap timing templates.
    "-I, --attack-interval": (Default: 0ms) Set custom interval after which an attack attempt without an answer should give up. It's recommended to increase it when attempting to scan unstable and slow networks or to decrease it on fast and reliable networks.
    "-T, --timeout": (Default: 2000ms) Set custom timeout value after which an attack attempt without an answer should give up. It's recommended to increase it when attempting to scan unstable and slow networks or to decrease it on fast and reliable networks.
    "-r, --custom-routes": (Default: <CAMERADAR_GOPATH>/dictionaries/routes) Set custom dictionary path for routes
    "-c, --custom-credentials": (Default: <CAMERADAR_GOPATH>/dictionaries/credentials.json) Set custom dictionary path for credentials
    "-o, --nmap-output": (Default: /tmp/cameradar_scan.xml) Set custom nmap output path
    "-d, --debug": Enable debug logs
    "-v, --verbose": Enable verbose curl logs (not recommended for most use)
    "-h": Display the usage information

Format input file

The file can contain IPs, hostnames, IP ranges and subnetwork, separated by newlines. Example:

0.0.0.0
localhost
192.17.0.0/16
192.168.1.140-255
192.168.2-3.0-255

Environment Variables
CAMERADAR_TARGET

This variable is mandatory and specifies the target that cameradar should scan and attempt to access RTSP streams on.

Examples:

    172.16.100.0/24
    192.168.1.1
    localhost
    192.168.1.140-255
    192.168.2-3.0-255

CAMERADAR_PORTS

This variable is optional and allows you to specify the ports on which to run the scans.

Default value: 554,5554,8554

It is recommended not to change these except if you are certain that cameras have been configured to stream RTSP over a different port. 99.9% of cameras are streaming on these ports.
CAMERADAR_NMAP_OUTPUT_FILE

This variable is optional and allows you to specify on which file nmap will write its output.

Default value: /tmp/cameradar_scan.xml

This can be useful only if you want to read the files yourself, if you don't want it to write in your /tmp folder, or if you want to use only the RunNmap function in cameradar, and do its parsing manually.
CAMERADAR_CUSTOM_ROUTES, CAMERADAR_CUSTOM_CREDENTIALS

These variables are optional, allowing to replace the default dictionaries with custom ones, for the dictionary attack.

Default values: <CAMERADAR_GOPATH>/dictionaries/routes and <CAMERADAR_GOPATH>/dictionaries/credentials.json
CAMERADAR_SCAN_SPEED

This optional variable allows you to set custom nmap discovery presets to improve speed or accuracy. It's recommended to lower it if you are attempting to scan an unstable and slow network, or to increase it if on a fast and reliable network. See this for more info on the nmap timing templates.

Default value: 4
CAMERADAR_ATTACK_INTERVAL

This optional variable allows you to set custom interval to wait between each attack in order to stay stealthy. It's recommended to increase it when attempting to scan a network that might be protected against bruteforce attacks. By default, there is no interval, in order to make attacks as fast as possible

Default value: 0ms
CAMERADAR_TIMEOUT

This optional variable allows you to set custom timeout value after which an attack attempt without an answer should give up. It's recommended to increase it when attempting to scan unstable and slow networks or to decrease it on fast and reliable networks.

Default value: 2000ms
CAMERADAR_LOGGING

This optional variable allows you to enable a more verbose output to have more information about what is going on.

It will output nmap results, cURL requests, etc.

Default: false
