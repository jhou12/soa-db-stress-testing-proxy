# Amazon-Styled SOA Proxy Server

Proxy that reroutes API requests from a Service-Oriented Architecture service, modeled from the Amazon Photos service. Can be deployed via Amazon EC2. Built with Express and Node, and tested with New Relic, Artillery, & Loader.io.

<p align="center">
<img src="screenshot.png" width="600"/>
</p>

## Setup on Local

Before setting up proxy locally, complete local setup for [Amazon Photos Database](https://github.com/jhou12/soa-db-stress-testing).

1. `git clone`
1. install dependencies with `npm install`
1. in `public/index.html`, make sure `localhost` script tag is active, and EC2 script tag is commented out
1. start server with `npm run start`
1. verify app up and running at `localhost:3000/:id` (for id values 1,000-10,001,000)

## Setup on EC2: Installation

Before setting up proxy on EC2, complete EC2 setup for [Amazon Photos Database](https://github.com/jhou12/soa-db-stress-testing).

1. create and launch EC2 instance with Ubuntu AMI on t2 micro (or preferred settings)
1. edit Instance Inbound Rules by adding "All Traffic" from "Anywhere," or preferred security settings
1. add pem key to root and .gitignore, and in Terminal make pem private with `chmod 400 [.pem file]`
1. connect to instance with `ssh -i "[pemKeyName].pem" ubuntu@[Public IPv4 DNS]`
NOTE: Public IPv4 address will change every time instance shuts down. To avoid repeated IP changes during development, restart instance instead of shutting down.
1. install Node on Ubuntu with
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
```
6. install Git on Ubuntu with `sudo apt-get install`
1. `git clone` repo
1. install dependencies with `npm install`
1. in `public/index.html`, make sure EC2 script tag is active, and `localhost` script tag is commented out
1. start server with `npm run start`
1. verify app up and running at url `http://[Public IPv4 DNS]:3000/:id` (for id values 1,000-10,001,000).
NOTE: must use `http`, not `https`, or will return error
1. to stop instance, go to instance page and click Instance State > Stop Instance.

## Setup on EC2: Stress Testing

1. for local stress testing data visualization, install New Relic: create a free account on [newrelic.com](https://newrelic.com/), and [install New Relic for Node](https://docs.newrelic.com/docs/agents/nodejs-agent/installation-configuration/install-nodejs-agent/) by installing New Relic dependency, downloading the newrelic.js config file in root, and requiring it in the server
1. to run stress test locally, install Artillery with `npm install -g artillery@1.6`, generate the test ids csv file with `node stressTest/writeTestIds.js`, and use `artillery run stressTest/reqTest.yml`
1. to run stress test in cloud, [install Loader.io](https://loader.io/) by signing up for a free account, adding a token to root, replacing token request endpoint in `server/index.js`, and running a test

## Related Projects:
- [Amazon Photos Database](https://github.com/jhou12/soa-db-stress-testing)

