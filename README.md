# Stat Dash

- [Stat Dash](#stat-dash)
  - [Introduction](#introduction)
  - [Installation](#installation)
    - [TL;DR](#tldr)
    - [The Deets...](#the-deets)
      - [Clone this directory](#clone-this-directory)
      - [Init and update the git submodules](#init-and-update-the-git-submodules)
      - [Spin up postgres](#spin-up-postgres)
      - [Add of refresh the Roit API key](#add-of-refresh-the-roit-api-key)
      - [Run the backend](#run-the-backend)
      - [Run the frontend](#run-the-frontend)
  - [Caveats](#caveats)


## Introduction
Submission for Full-Stack Take-Home Challenge: League of Legends Match Stats Dashboard

this project depends on an environment which supports 
* nodejs 20.11.0
* erlang 26.2.1
* elixir 1.16.1-otp-26

if you are familiar `asdf` there is a [.tool-versions](./tool-versions) file in the root of this project which will install the required versions by running `asdf install` from this directory.

The set up instructions below depend on docker, asdf, and git 

## Installation
### TL;DR
 * clone this directory
 * init and update the git submodules
 * build and run the docker image found in [stat-dash-postgres](./stat-dash-postgres/)
 * Add or refresh the API key for the Roit API
 * run the backend from the root of [stat-dash-back](./stat-dash-back/)
 * run the frontend from the root of [stat-dash-web](./stat-dash-web/)
 * navigate to http://localhost:3000 and search for some summoners 


### The Deets...
#### Clone this directory 
```
git clone https://github.com/BenGlasser/stat-dash.git
```
#### Init and update the git submodules
```
git submodule init && \
git submodule update
```
you should now have all the source you need to run the project.

#### Spin up postgres
Next you will need to start the postgres instance.  
```
docker compose up postgres -d 
```
   This instance runs the default port `5432` so if you have any local instances of postgres running there will be a conflicts.  This can be mitigated in several ways
   * shutting down your local instance (probably the easiest)
   * remapping the port binding in [docker-compose.yaml](https://github.com/BenGlasser/stat-dash/blob/main/docker-compose.yaml#L20)
   * altering the configs in [stat-dash-back](./stat-dash-back) to connect to a local instance of postgres
#### Add of refresh the Roit API key 
I've left my API key in here which is probably a bad idea, but it's only good for 24 hours so chances are you'll need to refresh it at some point.  The the location where you'll want to add it can be found here  

 👉👉👉 https://github.com/BenGlasser/stat-dash-back/blob/b4114454a7d3ad467633d322ec45aa0415df63b7/config/config.exs#L29


you'll notice that this config is also set up to read the environment variable `RIOT_API_KEY` so you can export your key before you compile the backend if you prefer.

#### Run the backend
Its time to run the phoenix server 

navigate to the root of [stat-dash-back](./stat-dash-back) and initialize the database 
```
mix ecto.create && mix ecto.migrate
```
Then fire up the phoenix server with
```
mix phx.server
```

If you don't see any errors you're good to go.

#### Run the frontend

navigate to  [stat-dash-web](./stat-dash-web/) and run the following commands
```
npm install && npm start
```
with any luck the setup is complete and you should be in business 🤞

You should be able to view stat-dash in all its glory at http://localhost:3000

there should also be a graphiql server running at http://localhost:4000/gql/GraphiQL

and a liveview server for viewing backend health at http://localhost:4000/dev/dashboard/home

## Caveats
* The summoner search currently only works for the north american region.  So if a search for a summoner doesn't return any results it's likely that summoner is located in another region.
* the database isn't used currently.  the project will probably work fine without it, but ecto is going to yell at you insescently that it is unable to conect.
* There is no rate limiting in here so if you search fast enough you could possible get capped out.  I don't remember what the limits are but I think you'd have to type pretty fast to make that happen.