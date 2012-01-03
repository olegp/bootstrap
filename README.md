# Bootstrap

### Dependencies

These instructions assume Ubuntu is used. Installation of dependencies may vary on other platforms. These steps need to be performed only once before starting to use Bootstrap.

Install Git:

    apt-get install git-core


Make sure you have the [EC2 API](https://help.ubuntu.com/community/EC2StartersGuide) command lines tools installed:

    apt-get install ec2-api-tools
    

### Setting up a Project

Create a new directory with the following subdirectories: `config`, `scripts`

Install Bootstrap with `git clone git://github.com/olegp/bootstrap.git`.

In the AWS Management Console, click your name in the upper right and select "[Security Credentials](https://aws-portal.amazon.com/gp/aws/developer/account/index.html?ie=UTF8&action=access-keyp_endpoint)". 
Click the X.509 Certificates tab and "Create a new Certificate". Download the two certificate files to the `config` directory and rename them from `pk-XXXX.pem` to `pk.pem` & `cert-XXXX.pem` to `cert.pem`.

Back in the Management Console, select the EC2 tab, NETWORK & SECURITY, Keypairs. Create a new keypair named "default" and save it to the `config` directory as well. Make sure this private key has the right permissions set with `chmod 700 config/default.pem`.

### Defining a Role

Create a file in the root of your project directory named e.g. `test` with the following contents:

    run hostname

That's it! You've defined a role called `test`, which will run the command `hostname` on an instance.


### Instantiating & Bootstrapping an Instance

To provision a new instance and apply the `test` role to it, run:

    ./bootstrap/bootstrap $(./bootstrap/instantiate) test
    
If all went well, you should get the hostname of the newly created instance. Creating a new instance and booting it takes about a minute, so please be patient. If any problems occur, Bootstrap will terminate immediately with an appropriate error message and will not continue.

Note that you actually ran two commands, one to instantiate the instance, which returns the hostname & another to run the `hostname` command on that instance.

### License 

(The MIT License)

Copyright (c) 2011+ Oleg Podsechin

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

