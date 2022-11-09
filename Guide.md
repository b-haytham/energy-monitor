# Energy Monitor Guide

This guide will walk you through the application. make sure you've [setup the envirement](/README.md#installing) first.

## Create resources

First login as super admin http://localhost:3001/admin/auth/login .

<br/>

To create a device first you need to create a subscription which requires an administrator with `Super User` role.

### Create a Super User

1. Navigate to users page by clicking on **Users** in the sidebar.
2. Click on the plus icon on the top right of the page.
<br>
3. Fill the form and click submit. make sure the role is `Super User`

### Create a Subscription

1. Navigate to the subscriptions page by clicking on **Subscriptions** in the sidebar.

2. Click on the plus icon on the top left of the page.

3. Fill the form and click submit 


### Create Device

you can create a device either by navigating to the subscription detail page or by following the same steps above.

1. In the subcriptions table click on more icon and click on **view**. <br> you can click on the arrow top left to see more info on the subscription

2. Click on + icon top left to create a device or a user  

3. Fill the form and click submit. <br> you can see more info on the device by selecting the device on bottom of the page. <br>
you can navigate to the device detail page either by clicking on the link icon in the bottom or by clicking on the device name in the top info section


## Insert values to database

We'll use a [cli](https://github.com/b-haytham/energy-monitor-cli) to seed the database. <br>
copy the env file.
```sh
cp envs/examples/cli.example.env envs/cli.env
```


### copy config file
```sh
cp config/examples/seed_config.example.yaml config/seed_config.yaml
```

### update config file 
In the dashboard navigate to the device detail page and copy `ID` and `Subscription ID` and change the values in the config.
<br>
this configuration will go back 12 months and every 15s will insert a random value in range (0.0..2.0) for `p` (power kw).

```yaml
# config/seed_config.yaml

devices: 
    # (mongo objectid) device subscription_id
  - subscription_id: <SUBSCRIPTION_ID>

    # (mongo objectid) device id 
    device_id: <DEVICE_ID>

    # (optional) name
    device_name: "dev1"

    # device value to seed
    value_name: "p"

    # Random min
    min: 0.0

    # Random max
    max: 2.0

    # "Add" :whether the to be added to the previous value | "Random": randomly generated values
    value_create_option: "Random"

    # how many month the device has been sending data
    months: 12

    # interval in seconds between 2 device messages
    interval: 15

```

### run 

```sh
docker run --env-file=envs/cli.env -v $(pwd)/config:/app/configs --network host bhaytham/energy-monitor-cli db seed 
```
refresh the page.
<br>
you can insert values for multiple devices in parallel. just add another entry and change the configuration

### delete inserted values

```sh
docker run --env-file=envs/cli.env -v $(pwd)/config:/app/configs --network host bhaytham/energy-monitor-cli db drop
```


## Publish messages.


### copy config file

```sh
cp config/examples/publish_config.example.yaml config/publish_config.yaml
```

### update config file

Like in seed config grab the `ID` of the device and `TOKEN` from the dashboard.<br> 

```yaml
# config/publish_config.yaml

devices: 
  - id: <DEVICE_ID>
    token: <DEVICE_JWT_TOKEN>
    topic: "device/notification"
    interval: 2 #seconds
    repeat: true
    values:
      - name: "s"
        value: 1
      - name: "u1"
        min: 0.0
        max: 250.0
      - name: "u2"
        min: 0.0
        max: 250.0
      - name: "u3"
        min: 0.0
        max: 250.0
      - name: "i1"
        min: 0.0
        max: 2.0
      - name: "i2"
        min: 0.0
        max: 20.0
      - name: "i3"
        min: 0.0
        max: 20.0
      - name: "p"
        min: 0.0
        max: 4.0
```
the config above will be converted by the cli to a json format and will be sent using mqtt.

```json
// converted message
{
  "d": "<DEVICE_ID>",
  "p": {
    "u1": 120 // between 0.0 and 250.0
    // ----
  },
  "t": "2022-08-11T01:45:45Z" //timestamp
}
```

### run

```sh
docker run --env-file=envs/cli.env -v $(pwd)/config:/app/configs --network host bhaytham/energy-monitor-cli mqtt pub  
```

Alternatively you can use other mqtt clients. (ex: [mosquito_pub](https://mosquitto.org/man/mosquitto_pub-1.html)).

```sh
mosquitto_pub -h localhost -p 1883 -i <DEVICE_ID> -u <DEVICE_ID> -P <DEVICE_TOKEN> -t device/notification \ 
-m "{\"d\": \"<DEVICE_ID>\", \"p\": { \"s\": 1, \"u1\": 200, \"u2\": 180, \"u3\": 180,\"i1\": 10,\"i2\": 20, \"i3\": 15,\"p\": 20 }, \"t\": \"2022-08-11T01:45:45Z\" }"
```
