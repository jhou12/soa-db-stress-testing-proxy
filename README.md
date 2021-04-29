# Amazon-Styled SOA Proxy Server

Proxy that reroutes API requests from a Service-Oriented Architecture service, made with Express and Node js. modeled from the Amazon Photos service. The database seeds and persists data for 10 million unique product IDs, and can be deployed via Amazon EC2. Built with Express, Node, and PostgreSQL, tested with New Relic, Loader.io, and Artillery, and optimized with Redis.


<p align="center">
<img src="screenshot.png" width="600"/>
</p>

## Setup on Local

1. `git clone`
1. install dependencies with `npm install`
1. write csv data with `npm run pg`
1. save csv data with `node seeding/pgSave.js`
1. start server with `npm run start`
1. build bundle with `npm run build`
1. make sure `server/index.js` and `client/src/request.js` are requesting `localhost` and not EC2 endpoints
1. verify app up and running at `localhost:4002/:id` (for id values 1,000-10,001,000)
1. for development, start webpack with `npm run react-dev`

## Setup on EC2: Installation

Create 2 EC2 instances (for service & database) following the same process for each:

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
1. install PostgreSQL with `sudo apt install postgresql postgresql-contrib`
1. install dependencies with `npm install`

## Setup on EC2: For database only

1. log into super user with `sudo su`, create root directory for csv files, and allow write access to directory with `chmod 777 [csv directory]`
1. copy csvs from local to EC2 directory, from local terminal: `scp -i "[pem key]" [absolute path]/*.csv ubuntu@[Public IPv4 DNS]:/[EC2 csv directory absolute path]`
1. log into the Postgres user with `sudo -u postgres psql` and save each csv into the db with `COPY "AllUrls" FROM '/[csv directory]/[file].csv' WITH DELIMITER ',' CSV HEADER;`
1. after saving all csv data to db, delete csv files to free up memory with `sudo su` and then `rm -R [csv dir]`
1. index database entries by logging into pg user with `sudo -u postgres psql` and using `CREATE INDEX "primaryQuery" ON "AllUrls"("assocId");`
1. change Postgres config to allow queries from external requests by finding config file using `sudo -u postgres psql -c 'SHOW config_file'` and for `pg_hba.conf` add last line `host all all 0.0.0.0/0 md5` and in `postgresql.conf` change `listen_addresses` to `*`

## Setup on EC2: Run App

1. make sure `server/index.js` and `client/src/request.js` are requesting EC2 and not localhost endpoints, and update EC2 IPv4 addresses in `server/index.js`, `client/src/request.js`, and `server/index_service.js`
1. start server with `node server/index_service.js` (for service) or `node server/index_db.js` (for database)
1. verify app up and running at url `http://[Public IPv4 DNS]:4002/:id` (for id values 1,000-10,001,000).
NOTE: must use `http`, not `https`, or will return error
1. for local stress testing data visualization, install New Relic: create a free account on `newrelic.com`, and [install New Relic for Node](https://docs.newrelic.com/docs/agents/nodejs-agent/installation-configuration/install-nodejs-agent/) by installing New Relic dependency, downloading the newrelic.js config file in root, and requiring it in the server
1. to run stress test locally, install Artillery with `npm install -g artillery@1.6`, generate the test ids csv file with `node stressTest/writeTestIds.js`, and use `artillery run stressTest/reqTest.yml`
1. to run stress test in cloud, [install Loader.io](https://loader.io/) by signing up for a free account, adding a token to root, and running a test
1. to stop instance, go to instance page and click Instance State > Stop Instance.

## Setup with Proxy

Complete local or EC2 setup above, and follow setup instructions for [proxy](https://github.com/jhou12/soa-db-stress-testing-proxy).

