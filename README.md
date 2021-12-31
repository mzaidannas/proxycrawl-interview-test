# Webscraper in ruby microservices

Basically we have 3 services

 1. **Scheduler**
 2. **Crawler**
 3. **Api**

 **Scheduler** is responsible for sending url to be scraped to crawler service via [rabbitmq](https://www.rabbitmq.com/)
 **Crawler** picks up urls from rabbitmq and processes them according to the processors defined in it. A processor can also send additional urls to rabbitmq to be processed through separate processor.
 After crawling pages. HTML is parsed according to processor defined and data is stored into [postgresql](https://www.postgresql.org/)
 **Api** is a REST service on top of that database that serves content in json format

# Technologies used

 - [**Ruby on Rails**](https://rubyonrails.org/)

> Web framework to be used in API and Crawler services.
 - [**Rabbitmq**](https://rubyonrails.org/)
> Message broker used as a communication medium between multiple ruby microservices
 - [**postgresql**](https://www.postgresql.org/)
> Data storage used to store and index amazon products data
 - [**Docker**](https://www.docker.com/)
> Used for local development setup and well as running all the services combined. Can also help in production deployment and autoscaling
# Concepts Implemented
 - Micro Service Architecture
 - Ruby scripting
 - Signal Trapping
 - Scheduled workers
 - Long running ruby processes
 - Ruby async programming
 - HTTP streaming (Huge json responses)
 - GIN(General Inverted Indices) for text searching
 - BRIN (Block Range Indices) for date range quering
 - Hash indices for exact matching
 - Background workers(Sneaker rabbitmq client)
 - Product categories with hierarchy system(Parent client categories)
 - Falcon async web server (Uses nodejs like event loop for non blocking request/response cycle)
 - Batch async http calls (I/O wait minimized by using multiple http calls in parallel)
 - SQL batch inserts/updates for better performance

# Local setup and running
Install docker on your system
 - **MacOS**
> Install homebrew package manager
> ```sh
> /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
> ```
Install docker desktop and git with brew cask
> ```sh
> brew install git
> brew cask install docker
> ```

- **Windows**
> Install/Update windows package manager winget (Update app installer package from windows store)

Install docker desktop and gitusing winget
> ```sh
> winget install -e --id Git.Git
> winget install -e --id Docker.DockerDesktop
> ```

- **Ubuntu**

Install docker and git using apt package manager
> ```sh
> sudo apt purge -y docker docker-engine docker.io containerd runc
> sudo apt install -y \
> apt-transport-https \
> ca-certificates \
> curl \
> gnupg-agent \
> software-properties-common
> curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
> echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
> sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io git
> sudo systemctl restart docker
> sudo systemctl enable docker
> sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
> sudo chmod +x /usr/local/bin/docker-compose
> sudo groupadd docker
> sudo usermod -aG docker $USER
> newgrp docker
> ```

Fetch code using git
```sh
git clone --recurse-submodules -j8 https://github.com/mzaidannas/proxycrawl-interview-test.git
```
Move to project directory
```sh
cd proxycrawl-interview-test
```
Create env file with required environment variables
```sh
tee .env << ENV
RUBY_OPT='--yjit'
RAILS_ENV='development'
LOG_LEVEL='debug'
DB_HOST='postgres'
REDIS_URL='redis://redis:6379/0'
AMQP_URL='amqp://guest:guest@rabbitmq:5672'
PROXYCRAWL_TOKEN='EfuuQyxFeOlppV4t5Z5gRQ'
ENV
```
Run project
```sh
docker-compose up scheduler crawler api
```

Check products through browser on url
`http://localhost:3000/products`
