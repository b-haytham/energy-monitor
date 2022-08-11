# Energy Monitor

Energy Monitor is an application to process, analyze, store and monitor energy usage. 

> :warning: This project is incomplete and is not for production.  <br>

![Energy monitor dashboard](/assets/energy-monitor-main.png)


## Description

### Components

* [web](https://github.com/b-haytham/energy-monitor-web): dashboard (admin/users) to manage resources.  
* [backend](https://github.com/b-haytham/energy-monitor-backend): monolith handle everything (cron jobs, alerts, users, email notification ..)
* [hivemq extension](https://github.com/b-haytham/energy-monitor-hivemq-extension): plugin written with the help of [hivemq extension sdk](https://www.hivemq.com/docs/hivemq/4.8/extensions/introduction.html) to authenticate devices and to react to device connection lifecyle
* [cli](https://github.com/b-haytham/energy-monitor-cli): tool to help seed/drop database and pub/sub to mqtt topics in development


### Features

* Realtime visualisation of device messages (power, voltage, current..)
* daily, monthly, annual power consumption
* Automatically generated monthly reports and bill estimation
* Alerts


## Getting Started

### Dependencies 

* [Docker](https://docs.docker.com/engine/install/) 
* [docker compose](https://docs.docker.com/compose/install/compose-plugin/)

### Installing

```sh
git clone --recurcive https://github.com/b-haytham/energy-monitor 
```

### Run

copy the example envirement variables

```sh
cd energy-monitor

cp envs/examples/backend.example.env envs/backend.env
cp envs/examples/hivemq.example.env envs/hivemq.env
cp envs/examples/dashboard.example.env envs/dashboard.env
```

change super admin credentials or leave the default

```sh
SUPER_USER_EMAIL=superadmin@gmail.com
SUPER_USER_PASSWORD=superadmin
```

optionally set email configuration

```sh
SMTP_HOST=
SMTP_PORT=
SMTP_USER=
SMTP_PASSWORD=
SMTP_FROM_ADDRESS=No Reply <engy-monitor@mail.com>
```


run docker compose

```sh
docker compose up
# --- or --- (depends on how you installed docker compose) 
docker-compose up
```
<br/>

| Name| Url |
| --- | --- |
| Dashboard | http://localhost:3001 |
| Admin login | http://localhost:3001/admin/auth/login |
| User login | http://localhost:3001/auth/login |
| backend Api | http://localhost:3000 |
| Socket io url | http://localhost:3000/socket.io
| Mqtt broker (hivemq) | tcp://localhost:1883


now navigate to http://localhost:3001/admin/auth/login <br/> and use email & password you setup in previous step.

<br/>

your can follow more detailed guide on how to navigate the application [GUIDE](/Guide.md).


## Licence
This project is licensed under the MIT License - see the [LICENCE](/LICENCE) file for details



 



