# Desenvolvimento de Aplicações Seguras
## Threat Modeling

### Document Version History

| Version  | Notes           | Date       | By |
| -----   | -----          | -----     | ----- |
| 1.0      | Initial release | 2017-03-26 | Jorge Martins |

### Application Information

| Information  | Value      |
| -----          |  -----  |
| Application Name |  GeoWS |
| Application version |  1.0 |
| Description | A Web Service that provides geographic information |
| Document Owner | Jorge Martins [<jorgemfm@gmail.com>] |
| Document Reviewer | Pedro Carneiro [<pedromcarneiro@gmail.com>] |

The GeoWS provides the users a RESTfull Web Service to get geographic information.
The current version, besides the authentication, provides only methods to get a list of countries and a list of cities.


### Software arquitecture diagram
![](sad.png)

<div class="page-break"></div>

### Used technologies

|  Technologies  |  Implementation Details  |
|  :-----          |  :-----          |
| PHP 7.x | PHP is the programming language the Web Services will be written |
| Laravel 5.4 | Laravel is a PHP framework and will be used to develop the the RESTfull Web Services |
| Apache 2.4 | Used as the web server to serve the Web Services |
| JSON Web Tokens | Used to secure the requests made to the Web Services |
| MySQL 5.7.x | All the information available will be stored in a database in MySQL  |
| Secure Sockets Layer | Will be used to encrypt HTTP traffic between the external apps and the web server |


To summarize:

The GeoWS will be developed using the [Laravel 5.4](https://laravel.com/) framework running on an [Apache 2.4.x](https://httpd.apache.org/) server with [PHP version 7.x.](http://php.net/)

The application will provide a RESTfull Web Service that returns the requested data in [JSON](http://www.json.org/) format.

The application will use [JSON Web Tokens](https://jwt.io/) for authentication and for each subsequent request.

### Application Decomposition

The GeoWS will only accept requests for data after the client has been authenticated.
After a succesfully authentication a token will be generated on the backend and returned to the client.
The token must be provided for every call to the GeoWS or it will issue a HTTP 403 Forbidden code.

The GeoWS only allows the clients to read data, so it will only responds to the standard HTTP Verb GET except for the autentication where it only accepts a the HTTP Verb POST

The following URL are available:

|  URL  |  Description  |
|  :-----          |  :-----          |
| /authenticate  |  URL for the authenticate process  |
| /countries | URL to get the list of countries  |
| /cities |  URL to get the list of cities |


### External Dependencies

|  ID  | Description  |
| :-----          |  :-----          |
| 1 | The Application will be served by apache on a Linux Server (Ubuntu 16.04 LTS). The Server will only have a minimum services available to minimize the threats |
| 2 | The database server will be MySQL (latest version) and will also be on a Linux Server (Ubuntu 16.04 LTS). The Server will only have a minimum services available to minimize the threats. |
| 3 | The connection between the Web Server and the database server will be over a private network. |
| 4 | The Web Server is behind a firewall and the only communication available is TLS. The database server will not have internet access !

<div class="page-break"></div>

### Entry Points

| ID  |  Name  |  Description  |  Trust levels  |
| :-----          |  :-----          |  :-----          |  :-----          |
| 1 | HTTPS Port | The GeoWS will only be available throught HTTPS | (1) Anonymous user <br> (2) User with Valid Login Credentials <br> (3) User with Invalid Login Credentials  |
| 2 |  Authentication URL | Every client muyst login before using the GeoWS |  (1) Anonymous user <br> (2) User with Valid Login Credentials <br> (3) User with Invalid Login Credentials |
| 3 |  Countries URL |  URL to get the countries list |  (2) User with Valid Login Credentials |
| 4 |  Cities URL |  URL to get the cities list |  (2) User with Valid Login Credentials |

### Assets
| ID  |  Name  |  Description  |  Trust levels  |
| :-----          |  :-----          |  :-----          |  :-----          |
| 1 |  Users |  Assets relating to the users. |   |
| 1.1 | User Login Details | The login credentials that a user will use to authenticate to the GeoWS. |(2) User with Valid Login Credentials<br> (4) Database Server Administrator<br>(5) Web Server User Process<br> (6) Database Read/Write User |
| 1.2 | Personal data | Some personal data are stored on the GeoWS database |(2) User with Valid Login Credentials<br>(4) Database Server Administrator<br>(5) Web Server User Process<br>(6) Database Read/Write User |
| 2 |  System | Assets relating to the underlying system. |   |
| 2.1 |  Availability |The GeoWS. | The GeoWS shoulb be available 24/7/365 and can be accessed by any user  | (4) Database Server Administrator<br>(7)Webserver Administrator<br> |
| 2.2 |  Ability to Execute Code as a Web Server User | This is the ability to execute source code on the web server as a web server user.  | (5) Web Server User Process<br>(7)Webserver Administrator<br> |
| 2.3 |  Ability to Execute SQL as a Database Read/Write User | This is the ability to execute SQL. Select, insert, and update queries on the database and thus have read and write access to any information stored within the GeoWS.  | (4) Database Server Administrator<br>(6) Database Read/Write User |
| 3 |  Web Services | Assets relating to the GeoWS |   |
| 3.1 |  Login Session | The login session of a user of the GeoWS | (2) User with Valid Login Credentials<br>  |
| 3.2 |  Access to the Databse Server | Access to the database server allows you to administer the database, giving you full access to the database users and all data contained within the database. | (4) Database Server Administrator  |

<div class="page-break"></div>

### Trust Levels
| ID     |  Name   |  Description  |
| :----- |  :----- |  :-----   |
| 1 |  Anonymous Web User | Any user that can try to connect to the GeoWS. |
| 2 |  User with Valid Login Credentials | Any user that has autheticated to the GeoWS with valid credentials. |
| 3 |  User with Invalid Login Credentials | Any user that has tried to autheticated to the GeoWS with invalid credentials. |
| 4 |  Database Server Administrator | The database server administrator has read and write access to the database . |
| 5 |  Web Server User Process | This is the user  that runs the process that the web server executes code |
| 6 | Database Read/Write User | The database user account used to access the database for read and write access.|


### Data Flow Diagrams
![](tm.png)

### Determine and Rank threats
#### STRIDE
| Type     | Examples   | Security Control |
| :----- | :----- | :-----   |
| Spoofing | Threat action aimed to illegally access and use another user's credentials, such as username and password. | Authentication |
| Tampering | Threat action aimed to maliciously change/modify persistent data, such as persistent data in a database, and the alteration of data in transit between two computers over an open network, such as the Internet. | Integrity |
| Repudiation | Threat action aimed to perform illegal operations in a system that lacks the ability to trace the prohibited operations. | Non-Repudiation |
| Information disclosure | Threat action to read a file that one was not granted access to, or to read data in transit. | Confidentiality |
| Denial of service | Threat aimed to deny access to valid users, such as by making a web server temporarily unavailable or unusable. | Availability |
| Elevation of privilege | Threat aimed to gain privileged access to resources for gaining unauthorized access to information or to compromise a system. | Authorization |

### Abuse Cases

![](abuse_case.png)

### DREAD
| Threat                                                |  D  |  R   |  E  |  A  |  D   | Rate | Risk   |
| :-----                                                | :-: | :--: | :-: | :-: | :-:  | :--: | :----: |
| Spoofing the External Web Application External Entity |  5  |  3   |  3  |  5  |  5   | 4.2  | <span style="color: gey;"><b>Low</b></span>    |
| SQL Injection Vulnerability for SQL Database          |  8  |  10  |  5  |  10 |  10  | 8.6  | <span style="color: red;"><b>High</b></span>   |
| Data Flow HTTPS Requests Is Potentially Interrupted   |  3  |  5   |  5  |  10 |  10  | 6.6  | <span style="color: orange;"><b>Medium</b></span> |
| Potential Process Crash or Stop for Web Service       |  3  |  4   |  3  |  10 |  5   | 5.4  | <span style="color: orange;"><b>Medium</b></span> |


### Security measures and vulnerabilities mitigation
#### STRIDE Threat & Mitigation
| Threat Type            | Mitigation |
| :-----------------     | :----- |
| Spoofing               | Both the web service and the database need authentication. After the client authenticated to the web service, esch resquest is validated
| Tempering              | Each request is validated for SQL Injection using proper validation technics like white list and sql espaping.<br> Every SQL Execution is run with prepared statments |
| Repudiation            | Every request is logged to the database |
| Information disclosure | Only authorized user have access to the Web Service.<br> The unse only has access to its personal information |
| Denial of service      | Currently we do not have a forma of mitigating DDoS Attacks |
| Elevation of privilege | Theres only one level of access to the web service, so there is no need to prevente this threat |


```
Created by: Jorge Martins [jorgemfm@gmail.com]
Date: 26-03-2017
Class: Administração de Sistemas e Serviços de Rede
Teacher: Pedro Carneiro [pedromcarneiro@gmail.com]
```
