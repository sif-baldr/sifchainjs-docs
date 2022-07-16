---
stoplight-id: hmam7nyn01ooe
---

# Sifchain - Data Services Startup Kit

# 

## Sif ETL (Extract Transform Load)

Deploy your own ETL server that extracts, transforms and loads sifchain data for analytics and application developments using your own database and `sif-apis` instance.

## Getting Started

First clone the ETL repo.

```bash
git clone https://github.com/sifchain/sif-etl.git
```

Then clone the API repo

```bash
git clone https://github.com/sifchain/sif-apis.git
```

## Setup the Prebuilt Docker Stack

ETL stack uses port 5490 (for TimescaleDB) and API stack uses port 8080 (for Web UI) so make sure there's no other services using those ports before starting the installation process.

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker compose](https://docs.docker.com/compose/install/)

  Verify that both are installed with the latest version:

  ```bash
  docker --version && docker-compose --version
  ```

  - docker version should be greater than 20.10.14
  - docker-compose version should be greater than 1.29.2
  - Some linux users may need to prepend commands with `sudo` depending on the [permissions](https://linuxfoundation.org/blog/classic-sysadmin-configuring-the-linux-sudoers-file) of docker executable.
  - Additional notes...

### Build Step

1. To build ETL full stack from local docker-compose environment. This will take few minutes depending on internet connection for initial run:

   ```bash
   cd sif-etl
   make up
   ```

2. To bring everything down and cleanup:

   `make down`

3. After Step #1, bring up the API services on separate terminal:

   ```bash
   cd ../sif-apis
   npm install
   npm run dev
   ```

4. Additional notes or updates.

## ETL Repo Code Structure

This repository is shaped as follows:

- `manager.py` is the commands manager, any command described in this file can be called using `python3 manager.py <COMMAND>` (replace `<COMMAND>` by the command name to run).
- `./src/commands` contains all the commands used by the manager
- `./src/events` contains all the event processors used by the event listener
- `./src/mutations` contains all mutations functions that update the database
- `./src/queries` contains all the queries functions to retrieve data from the database
- `./src/resolvers` contains components of the commands functions called resolvers
- `./src/utils` contains all utils functions used across the repository

## Installation

1. Install [python 3](https://phoenixnap.com/kb/how-to-install-python-3-ubuntu)
2. Install the other tools:

```bash
sudo apt install python3-pip python3-virtualenv
```

3. Setup a virtual environment using `virtualenv`:

```bash
virtualenv .
source bin/activate
```

4. Install the requirement packages:

```bash
pip3 install -r requirements.txt
```

5. Copy the dot env file and edit the new dot env file to fill up the required credentials:

```bash
cp -a .env-example .env
```

## Get Started

You can now run an ETL command using the command manager with the following command line:

```bash
python3 manager.py <COMMAND>
```

The list of command are provided in the next section.

## ETL Commands

Here are all the commands that you can run using the command manager:

- `tokenregistry`: retrieve latest changes in the on-chain token registry module and store to database.
- `external_price`: retrieve latest prices of all the available symbols and store to database
- `add_price_record`: retrieve latest pools data and compute symbol prices against rowan using the pools balances and store to database
- `add_price_record_pmtp`: retrieve latest pools swap prices and store to database
- `add_price_record_continuous`: run the command `add_price_record` continuously
- `refresh_pricespikes`: compute and store price spikes for rowan to database
- `update_once_day`: compute the highest daily block hight value and store to database
- `add_pool_record_continuous`: retrieve pools data and store to database
- `event_record_continuous`: retrieve latest blocks and parse and store all the events to database

## Database

The ETL server uses a timescale postgresql database with the tables defined in the schema file [here](https://github.com/Sifchain/sif-etl/blob/master/db/schema.sql).

