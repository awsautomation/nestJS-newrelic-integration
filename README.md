
# New Relic NestJs Integration Example App


### Introduction

This repo contains an example of two NestJs micoroservices (called parent and child) talking to each other over http on the same network (nest network).
They are dockerized, so it's a matter of running `docker-compose up` to deploy them to a local machine. (or to a remote server if you wish to)

Both parent and child apps contain New Relic APM agent (along with the configuration example) and are able to report metrics to a New Relic account

### App Architecture

![NestJs_NewRelic_architecture](https://user-images.githubusercontent.com/6328360/112493022-b0b2d000-8d79-11eb-812f-9622b0917f8e.png)

The above diagram shows the (dockerized) architecture of the app in this repo with the following components:
 * `nest network` - where all the microservices reside
 * `parent` container - to the left (port: 3000)
 * `child` container - in the middle (port: 3001)
 * booth `parent` and `child` have New Relic NodeJs Agent installed (and required by each of the microservices)
 
## How to run
 
### Prerequisites
* New Relic Account (sign up [here](https://newrelic.com/signup))
* New Relic License Key (find instructions [here](https://docs.newrelic.com/docs/accounts/accounts-billing/account-setup/new-relic-license-key/))
* `Docker` is installed on your machine.

### Setup
Find two `newrelic.ts` config file in `/src` directory inside each `parent` and `child`

Change two config variables in each file

```
app_name: ['YOUR_APP_NAME'],
license_key: 'YOUR_LICENSE_KEY'
```
to a desired `app_name` and paste your New Relic License Key acquired in previous steps to `license_key` variable.

### Start
Run `docker-compose up` and wait for all images to (build) install and start the app.

Once the app is started successfully yous should see a message in your terminal similar to the one below:
![Screenshot 2022-02-04 at 12 35 43](https://user-images.githubusercontent.com/6328360/152530083-320afff8-a383-4205-bc8a-3c3f6fd08e0e.png)

### Usage
The `parent` service has the following endpoints:
* GET `localhost:3000/`
  * just to confirm the app works
* POST `localhost:3000/cats/create`
  * to create a cat
  * with a json body payload containing 3 properties `name`, `age` & `breed`
    ```
    // for example 
    {
        "name": "Tom",
        "age": 3,
        "breed": "Persian"
    }
    ```
* GET `localhost:3000/cats/getAll`
  * to get all cats

The dependency between `parent` and `child` is that sending a request to `parent`, routes via `child` to `mongodb` and the request comes back the same route to the `parent`

Call any of these endpoints and you'll see a message (similar to the one below) in the console:
<img width="837" alt="Screenshot 2021-03-11 at 20 04 34" src="https://user-images.githubusercontent.com/6328360/110847596-0466e900-82a5-11eb-9286-4b465dac3710.png">

This particular example is a console log showing the flow of the `POST localhost:3000/cats/create` request. It's visible that `parent` calls `child` and gets a response back
intercepting `child`'s response carrying a `mongodb` record being created by `child`'s interaction with `mongodb` service.

### New Relic Observability Platform

Once you make a few calls, go to you New Relic account where the app data is being sent and navigate to `Explorer` and find the app (by the name you gave it to during the setup)
Go to `Distributed Tracing` and click on one of `Root Entry Spans`

You should be able to see `Distributed Tracing` spans captureg by New Relic Agents installed in both `parent` and `child`
<img width="1713" alt="Screenshot 2021-03-11 at 20 15 50" src="https://user-images.githubusercontent.com/6328360/110848964-96232600-82a6-11eb-80f1-75d9499ad1f2.png">
