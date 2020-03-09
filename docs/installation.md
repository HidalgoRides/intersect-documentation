# Installation
- [Installation](#installation)
    - [Prerequisites](#prerequisites)
    - [Installing Intersect Framework](#installing-intersect-framework)
    - [Development Environment](#development-environment)

<a name="installation"></a>
## Installation

<a name="prerequisites"></a>
### Prerequisites

It is recommended at your server meets the following prerequisites in order to properly develop on the Intersect Framework:
- PHP >= 7.1.3
- PDO PHP Extension

<a name="installing-intersect-framework"></a>
### Installing Intersect Framework

Intersect Framework manages its dependencies through [Composer](https://getcomposer.org/download/). Make sure you have Composer installed on your machine before running the following installation command.

    composer create-project --prefer-dist hidalgo-rides/intersect-framework my-project

<a name="development-environment"></a>
### Development Environment

#### Local using PHP's Built-In Server

If you have PHP installed on your machine, and the version is compatible, you can start a local PHP server by running the following commands:

    cd my-project/public
    php -S localhost:8080

You should now be able to access your local server by navigating to http://localhost:8080 in your browser of choice.

#### Local using Docker

If you are running Docker on your local machine, you can run the following command to start your local development environment. This will also install Docker instances of MySQL and PHPMyAdmin to allow for easier database development without having the hassle of configuring your local machine.

    docker-compose up -d

You should now be able to access your local server by navigating to http://localhost:8080 in your browser of choice. 

If you want to access PHPMyAdmin, you should be able to navigate to http://localhost:8081.