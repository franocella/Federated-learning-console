# Federated Learning Web Console (FLconsole)

This project, developed for the Distributed Systems and Middleware Technologies course at the University of Pisa, provides a web-based console for managing and monitoring Federated Learning (FL) experiments.

## 📝 Overview

The primary goal of this project is to offer a user-friendly graphical interface for running, monitoring, and analyzing Federated Learning experiments. Federated Learning is a decentralized machine learning technique where multiple clients collaboratively train a shared model while keeping their data localized.

This system uses an **FL Director**, an Erlang node, to coordinate the execution of experiments among participating devices. The platform is designed to support the concurrent execution of multiple experiments, provide real-time analytics, and offer flexible storage for experiment statistics.

## ✨ Key Features

  * **Web-Based Console**: A graphical interface to initiate, manage, and monitor FL experiments.
  * **Experiment Management**: Administrators can perform full CRUD (Create, Read, Update, Delete) operations on experiment configurations and the experiments themselves.
  * **User and Admin Roles**: The system supports two distinct roles:
      * **Users**: Can register, log in, search for experiments, and monitor their real-time progress.
      * **Admins**: Have full control over the system, including managing configurations, creating and running experiments, and viewing all experiment data.
  * **Real-Time Monitoring**: Utilizes WebSocket communication for real-time data exchange, allowing users to seamlessly track the progress of ongoing experiments.
  * **Concurrent Execution**: Leverages Java threads and `ExecutorService` to efficiently manage and run multiple experiments simultaneously.
  * **Flexible Data Storage**: Uses MongoDB (or a compatible DocumentDB) for storing experiment statistics, user data, and configurations, taking advantage of its flexibility and horizontal scalability.
  * **Centralized Analytics**: Provides a centralized access point for all experiment statistics, simplifying monitoring and analysis.

## 🏗️ System Architecture

The system is built upon a **Model-View-Controller (MVC)** architecture to ensure a clear separation of concerns and enhance maintainability.

The core components are:

  * **Java Web App**: The main application server built with the Spring Framework. It consists of:
      * **GUI (View)**: The user interface for interaction.
      * **Controller**: Handles user requests and orchestrates the application flow.
      * **Service**: Contains the business logic of the application.
      * **DAO (Data Access Object)**: Manages all interactions with the database.
      * **Erlang Message Handler**: A component that interfaces with the FLang infrastructure via the JInterface library.
  * **FLang Infrastructure**: The backend that manages the federated learning process.
      * **Director**: An Erlang node responsible for propagating experiment requests to the participating clients (User 1...n).

### Communication

  * **WebSockets**: For real-time communication between the frontend and the backend service.
  * **JInterface**: A Java library used to create a message handler that communicates with the Erlang-based FL Director for message passing. Messages are typically tuples with an atom for the type and a JSON string for the body.

## 🛠️ Development Environment & Technologies

  * **Backend**: Java, Spring Framework
  * **Middleware/FL**: Erlang
  * **Database**: MongoDB
  * **Real-time Communication**: WebSockets
  * **Build Automation**: Maven
  * **Version Control**: Git and GitHub
  * **IDE**: IntelliJ IDEA
  * **Testing**: JUnit

## ⚙️ System Configuration

### MongoDB Replica Set

1.  Launch a MongoDB instance for each replica using the following command structure:
    ```bash
    mongod --replSet <SETNAME> --dbpath <PATH> --port <PORT> --bind_ip localhost,<IP> --oplogSize 200
    ```
2.  Connect to the primary node:
    ```bash
    mongosh --host <PRIMARY_IP> --port <PORT>
    ```
3.  Define the replica set configuration:
    ```javascript
    rsconf = {
        _id: "<SETNAME>",
        members: [
            {_id: 0, host: "IP_1:PORT_1", priority: 1},
            {_id: 1, host: "IP_2:PORT_2", priority: 2},
            // ... other members
        ]
    };
    ```
4.  Initialize the replica set:
    ```javascript
    rs.initiate(rsconf)
    ```

### Tomcat

1.  Copy the generated `.war` file to the `webapps` directory of your Tomcat installation.
    ```bash
    scp /path/to/your/app.war user@REMOTE_IP:/path/to/tomcat/webapps/
    ```
2.  Start the Tomcat server:
    ```bash
    /path/to/tomcat/bin/startup.sh
    ```

### Nginx (as a Reverse Proxy)

1.  Open the Nginx configuration file:
    ```bash
    sudo nano /etc/nginx/nginx.conf
    ```
2.  Add a server block to proxy requests to Tomcat:
    ```nginx
    server {
        listen 80;
        server_name YOUR_SERVER_IP;

        location / {
            proxy_pass http://localhost:8080; # Assuming Tomcat runs on port 8080
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    ```
3.  Restart Nginx to apply the changes:
    ```bash
    sudo systemctl restart nginx
    ```

### EPMD (Erlang Port Mapper Daemon)

Start the EPMD service which is required for Erlang node communication:

```bash
epmd
```

Once these services are configured and running, the **FL Web Console** application will be ready for use.

## 👥 Authors

- [**Feyzan Çolak**](https://github.com/feyzancolak)

- [**Flavio Messina**](https://github.com/xDarkFlamesx)

- [**Francesco Nocella**](https://github.com/franocella)

## 📜 License

This project is licensed under the **Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License**. See the [LICENSE](http://creativecommons.org/licenses/by-nc-sa/4.0/) file for details.

This means the software is provided "as-is" and you are free to use, share, and adapt it for **non-commercial purposes**, as long as you provide appropriate attribution and distribute any derivative works under the same license.
