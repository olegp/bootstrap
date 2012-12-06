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
Click the "X.509 Certificates" tab and "Create a new Certificate". Download the two certificate files to the `config` directory and rename them from `pk-XXXX.pem` to `pk.pem` & `cert-XXXX.pem` to `cert.pem`.

Back in the Management Console, select the EC2 tab, "NETWORK & SECURITY", "Keypairs". Create a new keypair named "default" and save it to the `config` directory as well. Make sure this private key has the right permissions set with `chmod 600 config/default.pem`.

Also, make sure that your `default` security group allows inbound SSH connections. To do this, select Security Groups on the left, then select the `default` group in the main view, then the Inbound tab below. Next to "Create a new rule" select "SSH" in the dropdown, then "Add Rule" and "Apply Rule Changes".

### Defining a Role

Create a file in the root of your project directory named e.g. `test` with the following contents:

    run hostname

That's it! You've defined a role called `test`, which will run the command `hostname` on an instance.


### Bootstrapping Your First Instance

To provision a new instance and apply the `test` role to it, run:

    ./bootstrap/bootstrap $(./bootstrap/instantiate) test
    
If all went well, you should get the hostname of the newly created instance. Creating a new instance and booting it takes about a minute, so please be patient. If any problems occur, Bootstrap will terminate immediately with an appropriate error message and will not continue.

Note that you actually ran two commands, one to instantiate the instance, which returns the hostname & another to run the `hostname` command on that instance.

You may now want to terminate this instance via the AWS EC2 Management Console.

### Commands

  * instantiate [EC2_INSTANCE_TYPE] [EC2_REGION] - instantiates a new EC2 instance on AWS, waits for SSH to become available and prints its hostname, EC2_INSTANCE_TYPE can be `t.1micro`, `m1.small`, `m1.large` etc. and EC2_REGION is one of `us-east-1` or `eu-west-1`
  * bootstrap [USER@]HOST[:PORT] SCRIPT... - resolves scripts via BS_PATH and runs them locally to build up a script that runs remotely on HOST; this command is explained in more detail below

### Bootstrap Internals

Bootstrap's design is very simple. The `bootstrap` script runs on the client and executes all scripts provided to it as arguments (referred as "roles"). 

These role scripts build up a script that is then run remotely on the server. Because the role scripts are run within the context of the bootstrap script (via `source`), they have access to a number of Bash functions which make it easy to build up the remote script.

### Bootstrap Functions

The following function are available inside the role scripts:

  * **include SCRIPT...** - includes one or more other scripts that are run on the client; scripts are looked up in BS_PATH
  * **run COMMAND...** - adds one or more commands to be run on the server, with the Bash variables set on the client
  * **script SCRIPT...** - adds one or more scripts to be run on the server, with the Bash variables set on the server; scripts are looked up in BS_PATH
  * **put FILE PATH [CHOWN] [CHMOD]** - puts a local file on the remote server, optionally setting the user:group and permissions; the Bash variables are set on the server

### Bootstrap Options

It is possible to run the `bootstrap` command against more than one server by specifying them in a comma separated list, e.g.:

    ./bootstrap/bootstrap alpha,beta test

It is also possible to run a single command remotely, as follows:

    ./bootstrap/bootstrap alpha -c "date"

These can be combined to run a single command on multiple instances:

    ./bootstrap/bootstrap alpha,beta -c "date" 

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

