# 5G-CLARITY-AI-ENGINE - Based on OpenFaaS

The AI Engine is largely based on OpenFaaS, but provides additional ML monitoring functionality. And a template to deploy AI Function is also provided.

## Installation

0. Make sure that Docker Swarm is initialised.
1. Install the AI Engine version of OpenFaaS from [joemc94 GitHub](https://github.com/joemc94/aiengine).
2. Create username:
    ``` Bash 
    echo admin | docker secret create basic-auth-user -
    echo 12345 | docker secret create basic-auth-password -
    ```
3. Deploy the OpenFaaS stack via
	``` Bash
	docker stack deploy func -c docker-compose.yml
	```
4. Install faas-cli. Instructions [here](https://docs.openfaas.com/cli/install/)
4. Connect to Grafana and add Prometheus as a new datasource using the url [http://func_prometheus:9090](http://func_prometheus:9090).
5. Use the python3-aiengine template from the template folder to create a new function. The python3-aiengine template contains the new, more efficient of_watchdog and boilerplate code for the monitoring.
``` Bash
faas-cli new --lang python3-aiengine <name>
```
