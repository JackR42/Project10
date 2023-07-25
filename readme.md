Project 10

Creating two separate SQL Server instance containers inside Codespaces and configuring AlwaysOn Availability Groups requires some additional steps. Here is a step-by-step guide:

Step 1: Create the .devcontainer folder in the root of your project if it doesn't exist.

Step 2: Inside the .devcontainer folder, create a file named docker-compose.yml and open it in a text editor.

Add the following content to the docker-compose.yml file:

version: '3.8'
services:
  sqlserver1:
    image: mcr.microsoft.com/mssql/server
    ports:
      - 1433:1433
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=<password1>
      - MSSQL_AGENT_ENABLED=true
    networks:
      - alwaysOnNetwork
  sqlserver2:
    image: mcr.microsoft.com/mssql/server
    ports:
      - 1434:1433
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=<password2>
      - MSSQL_AGENT_ENABLED=true
    networks:
      - alwaysOnNetwork
networks:
  alwaysOnNetwork:

Make sure to replace <password1> and <password2> with strong passwords for the SQL Server instances.

Step 3: Save the docker-compose.yml file.

Step 4: Inside the .devcontainer folder, create a file named devcontainer.json and open it in a text editor.

Add the following content to the devcontainer.json file:

{
  "name": "SQL Server",
  "dockerComposeFile": "docker-compose.yml",
  "service": "sqlserver1",
  "extensions": [
    "ms-mssql.mssql"
  ]
}
This configuration specifies that the first SQL Server instance will be the default one used by the Codespaces environment.

Step 5: Save the devcontainer.json file.

Step 6: In the terminal of Codespaces, run the following command to start the containers:


docker-compose up -d
It will bring up both SQL Server containers.

Step 7: Connect to the SQL Server instance inside the first container using SQL Server Management Studio or any other SQL Server client using the following connection details:

Server name: localhost,1433
Authentication: SQL Server Authentication
Username: sa
Password: <password1>
Step 8: Connect to the SQL Server instance inside the second container using SQL Server Management Studio or any other SQL Server client using the following connection details:

Server name: localhost,1434
Authentication: SQL Server Authentication
Username: sa
Password: <password2>
Step 9: Set up and configure AlwaysOn Availability Groups following the official Microsoft documentation. This involves creating a Windows Server Failover Cluster (WSFC) and configuring the listener, availability group, and database mirroring.

Please note that configuring AlwaysOn Availability Groups is more involved and requires additional infrastructure setup. This guide only covers the containers' creation and initial setup.

I hope this helps you get started with creating multiple SQL Server instance containers and configuring AlwaysOn Availability Groups in your Codespaces environment!