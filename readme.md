Project 10

==== SOLUTION 2

ChatGPT prompt: inside codespace create 2 docker containers SQL1 and SQL2 with sqlserver instances and configure for AlwaysOn availability groups
ChatGPT response:

To create two Docker containers with SQL Server instances and configure them for AlwaysOn availability groups, you can follow these steps:

Install Docker: If you haven't already, install Docker on your machine. You can download and install Docker Desktop from the official Docker website (https://www.docker.com/products/docker-desktop).

Open your Codespace terminal: Launch your Codespace and open the terminal or command prompt.

Pull the SQL Server Docker image: Run the following command to pull the SQL Server 2019 image from the Docker Hub:

docker pull mcr.microsoft.com/mssql/server:2019-latest
Create the primary container (SQL1): Run the following command to create the primary SQL Server container (SQL1):
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=yourStrong@Password" \
   --name SQL1 -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest

Create the secondary container (SQL2): Run the following command to create the secondary SQL Server container (SQL2):
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=yourStrong@Password" \
   --name SQL2 -p 1434:1433 -d mcr.microsoft.com/mssql/server:2019-latest
Notice that we used a different host port (1434) for SQL2, which allows it to run alongside SQL1. Change the password if needed.

Verify the containers are running: Run the following command to check if the containers are running:
docker ps
You should see SQL1 and SQL2 in the container list.

Connect to SQL Server in the primary container: You can connect to the SQL Server instance in the primary container (SQL1) using SQL Server Management Studio (SSMS) or any other database tool. Use the following connection details:
Server: localhost,1433 (or your server IP if running remotely)
Authentication: SQL Server Authentication
Login: sa
Password: the password you set in step 4.
Configure AlwaysOn availability groups: Now that you have the containers running, you can configure the AlwaysOn availability groups using T-SQL or SSMS.
Here's an example T-SQL script to configure AlwaysOn availability groups:

Copy
Insert
New
-- Enable AlwaysOn availability groups on both instances
ALTER AVAILABILITY GROUP [MyAvailabilityGroup]
   ADD DATABASE MyDatabase;
GO

-- Configure endpoint on primary replica
ALTER AVAILABILITY GROUP [MyAvailabilityGroup]
   MODIFY REPLICA ON 'SQL1'
      WITH (ENDPOINT_URL = 'TCP://SQL1:5022');

-- Configure endpoint on secondary replica
ALTER AVAILABILITY GROUP [MyAvailabilityGroup]
   MODIFY REPLICA ON 'SQL2'
      WITH (ENDPOINT_URL = 'TCP://SQL2:5022');

-- Join secondary replica to the availability group
ALTER AVAILABILITY GROUP [MyAvailabilityGroup]
   JOIN WITH (CLUSTER_TYPE = NONE);
GO
Make sure to replace [MyAvailabilityGroup] with your desired availability group name and [MyDatabase] with your specific database name.

Test the availability group setup: After configuring the availability group, you can test the failover by stopping the primary SQL Server container (SQL1) and observing if the secondary SQL Server container (SQL2) becomes the new primary.

==== SOLUTION1

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

==== CONFIG
docker-compose up -d
docker stop $(docker ps -a -q)

devcontainer.json
{
    "name": "SQL Server",
    "dockerComposeFile": "docker-compose.yml",
    "service": "sqlserver1",
    "workspaceFolder": "/workspace",
    "customizations": {
      "vscode": {
        "extensions": [
          "ms-mssql.mssql"
        ]
      }
    }
}

==== MORE
https://github.com/rafaelrodrigues3092/docker-mssql-alwayson/tree/main
https://medium.com/spawn-db/codespaces-with-databases-50fdf3b73d25
https://github.com/microsoft/vscode-remote-try-sqlserver/tree/main

