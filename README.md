# LaravelDocker

## Installation:

1. inside the ./app folder put your Laravel project
2. inside ./app/.env you must have the following environment variables set:
 - APP_DOMAIN
 - PHP_VERSION
  
  PHP_VERSION variable can be 7.3, 7.4 or 8.0
  
 3. Nginx site can be edited in ./webserver/php*.*/nginx/default.conf
 
## Usage

- Start: **sh up.sh**
- Stop:  **sh down.sh**
