# InstruCRX_backend



This prototype aims to provide training for incident responders in a highly realistic scenario making use of the simulation component of the digital twin of an industrial filling plant. In the scenario, an attacker has gained access to the industrial system and performs a Man-In-The-Middle attack to disrupt the filling operations. The components of the industrial system thereby produce log data which are forwarded to a SIEM system. Completing the tasks of the cyber range, a trainee gains knowledge about the selected attacks on the industrial system and how to respond these attacks. This is achieved by investigating the corrensponding alarms and events in the SIEM and taking appropriate action to contain, erdicated and finally recover from the attack.


**User interface of the cyber range:**

 <p align="center">
  <img src="https://user-images.githubusercontent.com/56884203/189836470-c4a44d35-4940-49e7-bfa6-a2da1bb3b747.png" width="500" />
</p>


## Conceptual overview of the cyber range design

The cyber range consists for four main building blocks: a **Virtual Filling Plant**, a **SIEM-based SOC**, **Learning Management System (LMS)**, and a **REST-API**.


## Architecture of the prototype

The cyber range is contained within a Ubuntu-based Virtual Machine.

Two of the building blocks are realized through a micro-service architecture based on **Docker Containers**: 

- The **Virutal Filling Plant**, in turn, consists of three components contained in one Docker container:
  - a simulation of a [Digital Twin](./src) which is tailored to the needs of the cyber range scenario. It is implemented with [MiniCPS](https://github.com/scy-phy/minicps), an academic framework for simulating cyber-physical systems which builds upon [Mininet](http://mininet.org). 
  - simulated MITM attacks based on [Ettercap](https://www.ettercap-project.org/)
  - an Intrusion Dectection System (IDS) implemented with [Scapy](https://scapy.net/).
- The **SIEM-based SOC** system is realized with [Dsiem](https://www.dsiem.org/), which builds upon [Filebeat, Elasticsearch, Logstash and Kibana](https://www.elastic.co/). and comprises five Docker containers.

The remaining two building blocks run directly on the Ubuntu-based Virtual Machine.


- The **Learning Management System (LMS)** is implemented with the JavaScript Framework [Vue.js](https://vuejs.org/). The respective source code is stored in the [Frontend](https://github.com/InstruCRX/InstruCRX_frontend) repository of the project.
- A **[REST-API]((https://github.com/InstruCRX/InstruCRX_backend/tree/main/src/pyrest))** implemented with [Flask](https://flask.palletsprojects.com/en/1.1.x/) connects the LMS, the Digital Twin and the SIEM-System
- The user data is stored in a Firestore collection, described in detail [here](#user-data-management)

 <p align="center">
  <img src="https://user-images.githubusercontent.com/56884203/189820082-c1b67f1a-a2d5-4e98-ab14-fcaf3164c698.png" width="500" />
</p>



## Installation (for Ubuntu 20.04)

1. Install [Docker](https://docs.docker.com/engine/install/ubuntu/) and [Docker-Compose](https://docs.docker.com/compose/install/) as described in the respective docs

2. create a joint directory and clone the required repositories:

```bash
mkdir cyberrange && \
cd cyberrange && \
git clone git@github.com/InstruCRX/InstruCRX_backend.git && \
git clone git@github.com/InstruCRX/InstruCRX_frontend.git && \
git clone git@github.com/InstruCRX/InstruCRX_userManagement.git
```

  3. **Install dependencies for deployment of the front end:**

```bash
cd InstruCRX_frontend && \
bash setup_frontend.sh
```

4. **Install dependencies for deployment of the Flask-Api:**

```bash
cd InstruCRX_backend/src/host_vm && \
bash setup_cyberrange_host.sh
```

5. **Setup and start the cyber range**: This will start the microservice-infrastructure (Elasticsearch, Filebeat, Logstash, Kibana, Dsiem and Digital Twin), the cyber range front end (running on port 7080) and the API that connects both

```bash
cd deployments/docker && \
bash init_cyberrange.sh
```

6. Enter the ip address or hostname where the cyber range should be deployed. Usually, this is either the default ip address of the maschine or localhost. 199.999.9.99 is used as an example ip address here.

```bash
Enter the Hostname or IP Address where the cyber range will be deployed: 199.999.9.99
```

 7. To simplify initial deployment of the infrastructure, user management is deactivated by default. To enable multiple trainees to participate at the same time, please modify the variable loginDisabled in [Settings.js] (https://github.com/InstruCRX/InstruCRX_frontend/blob/main/src/Settings.js):

        loginDisabled: false //default false

    To set up user management, please refer to the detailed documentation of [InstruCRX_userManagement](https://github.com/InstruCRX/InstruCRX_userManagement).

    

## Shutdown

To shut down the infrastructure you can either the use the API-functionality **ht<span>tp://</span>199.999.9.99:9090/stop_cr** or run the shutdown script:

 ```bash
cd deployments/docker && \
bash docker_stop.sh
 ```

## Startup

To restart the infrastructure you can either the use the API-functionality **ht<span>tp://</span>199.999.9.99:9090/start_cr** or run the startup script:

 ```bash
cd deployments/docker && \
bash docker_start.sh
 ```
