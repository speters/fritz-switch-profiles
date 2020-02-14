![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)

# fritz-switch-profiles
A (Python) script to remotely 
 * set device profiles of an AVM Fritz!Box
 * print list of internet tickets
 * extend internet time for a device by redeeming an internet ticket

## Installation

```
git clone https://github.com/flopp/fritz-switch-profiles.git
cd fritz-switch-profiles
virtualenv -p python3 env
source env/bin/activate
pip install -r requirements.txt
```

## Usage

```
usage: fritz_switch_profiles.py [-h] [--url URL] [--user USER]
                                [--password PASSWORD] [--inifile INIFILE]
                                [--list-devices] [--list-profiles] [--tickets]
                                [--extend EXTEND]
                                [DEVICE=PROFILE [DEVICE=PROFILE ...]]

positional arguments:
  DEVICE=PROFILE       Desired device to profile mapping

optional arguments:
  -h, --help           show this help message and exit
  --url URL            The URL of your Fritz!Box; default: http://fritz.box
  --user USER          Login username; default: empty
  --password PASSWORD  Login password
  --inifile INIFILE    .ini config file holding username/password
  --list-devices       List all known devices
  --list-profiles      List all available profiles
  --tickets            Print internet tickets
  --extend EXTEND      extend internet for device by redeeming an internet
                       ticket
```

1. Determine the ID of the device, whose profile you want to change

```
./fritz-switch-profiles.py --password YOURPASSWORD --list-devices
```
->
```
LOGGING IN TO FRITZ!BOX AT http://fritz.box...
FETCHING AVAILABLE PROFILES...
FETCHING DEVICES...
FETCHING DEVICE PROFILES...

DEVICE_ID        PROFILE_ID       DEVICE_NAME
landevice5007    filtprof1        android-1234567890123456 [NOT ACTIVE]
landevice6494    filtprof1        my kid's iphone
landevice5006    filtprof2        Chromecast
...
```

2. Determine the available profiles
```
./fritz-switch-profiles.py --password YOURPASSWORD --list-profiles
```
->
```
LOGGING IN TO FRITZ!BOX AT http://fritz.box...
FETCHING AVAILABLE PROFILES...
FETCHING DEVICES...
FETCHING DEVICE PROFILES...

PROFILE_ID       PROFILE_NAME
filtprof1        Standard
filtprof2        Gast
filtprof3        Unbeschränkt
filtprof4        Gesperrt
```

3. Actually change the profiles
```
./fritz-switch-profiles.py --password YOURPASSWORD landevice6494=filtprof4
```
->
```
LOGGING IN TO FRITZ!BOX AT http://fritz.box...
FETCHING AVAILABLE PROFILES...
FETCHING DEVICES...
FETCHING DEVICE PROFILES...

UPDATING DEVICE PROFILES...
  CHANGING PROFILE OF landevice6494/my kid's iphone TO filtprof4/Gesperrt
```

Note that you may change the profiles of multiple devices at once by supplying multiple `DEVICE=PROFILE` pairs on the command line.

## Usage as a library

From [example.py](examples/example.py)

```python
from fritz_switch_profiles import FritzProfileSwitch

url = 'http://fritz.box'
user = ''
password = 'mysecurepassword'

fps = FritzProfileSwitch(url, user, password)
devices = fps.get_devices()
profiles = fps.get_profiles()

fps.print_devices()
fps.print_profiles()

profile_for_device = [devices[0]['id1'], profiles[2]['id']]

fps.set_profiles(profile_for_device)
```

## ini Config File
You can store your credentials in an .ini config file if it follows the following format:
```
[fritz.box]
host = http://fritz.box
username = MY_USERNAME
password = MY_PASSWORD
```

## Known Issues

- Non-uniquely named devices may confuse the script.

## License
[MIT](https://github.com/flopp/fritz-switch-profiles/blob/master/LICENSE) &copy; 2018 Florian Pigorsch & contributors
