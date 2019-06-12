---
published: false
---
---
layout: post
title: running rail app in docker
published: true
---

# Use Docker Compose to set up and run a Rails/MYSQL app

#### Step 1: 
##### First, using `Dockfile` to Define the project
we need to create Dockefile, you can cop sample 
dockerfile from offical websie
(This is sample [Dockerfile](https://docs.docker.com/compose/rails/) copy from docker offical website)

```
FROM ruby:2.5
RUN apt-get update -qq && apt-get install -y nodejs postgresql-client
RUN mkdir /myapp
WORKDIR /myapp
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock
RUN bundle install
COPY . /myapp

# Add a script to be executed every time the container starts.
COPY entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
EXPOSE 3000

# Start the main process.
CMD ["rails", "server", "-b", "0.0.0.0"]
```
##### second, create a Gemfile to just load rails. 

```
source 'https://rubygems.org'
gem 'rails', '~>5' # specify rails version in here

```
##### Third, create an empty `Gemfile.lock` To build `Dockefile`

 `touch Gemfile.lock`

##### Fourth, create shell script, named  `entrypoint.sh `
The purpose of entrypoint scipt is to fix a Rails-specific issue that prevents the server from restarting when a certain server.pid file pre-exists. 

```
#!/bin/bash
set -e

# Remove a potentially pre-existing server.pid for Rails.
rm -f /myapp/tmp/pids/server.pid

# Then exec the container's main process (what's set as CMD in the Dockerfile).
exec "$@"
```
##### Finaly,`create docker-compose.yml` file 

```
version: '3'
services:
  db:
    image: mysql
    volumes:
      - ./tmp/db:/var/lib/mysql/data
  web:
    build: .
    command: bash -c "rm -f tmp/pids/server.pid && bundle exec rails s -p 3000 -b '0.0.0.0'"
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
```
### Step 2: Build the project
Now it's time to generate the rails skelton app using docker compose run command:

`docker-compose run web rails new . --force --no-deps --database=postgresql`

If you ever modify gem file. which you'll do.
you need run: 

`docker-compose build`
### Step 3: Connect the database
This is tricky part. when I followed docker website. I was stuck in this step. the offical mysql docker image which is somehow can't initial. I got 

` [ERROR] --initialize specified but the data directory has files in it. Aborting.`  

After google for a while. I found a solution which is change db image source in `docker-compose.yml` file

from `image: mysql` to `image: mariadb`
the detail discuss about this issue is [here](https://github.com/docker-library/mysql/issues/69)

Now we can boot the app with `docker-compose up`:

`docker-compose up`
Finally, you need to create the database. In another terminal, run:
`docker-compose run web rake db:create`
 
 


