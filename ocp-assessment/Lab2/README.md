## Lab2

### Prerequisites

Please clone/fork this replo.

In order to start deploying this lab please replace your ID in the bellow line:

```
sed s,lab2,<your-id>-lab2,g lab2-resources.yaml | oc create -f -
```

### Instructions

In this lab you should be able to identify and resolve the issues in order to have a functional application.

Additional instructions:

- In order to validate Mysql instance please create a new application called `adminer` using this ENV variable pointing to mysql service: `ADMINER_DEFAULT_SERVER=<service_name>`
- Adminer is a web DB client, you can refer docker hub documentation in this [link.](https://hub.docker.com/_/adminer) - This can be deployed using any strategy you prefer.

**Results:** MySQL must be reachable using Adminer's route using correct credentials. (i.e. https://adminer-lab2.apps.poc-ocp.poc-ocp.bld.dst.ibm.com)
