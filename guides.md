Microsoft Open Hack Containers
Overview

OpenHack
Hello and welcome to OpenHack, a challenge oriented hack event from Microsoft. From here you will be presented with a series of challenges, each one more difficult than the one before.

You should already be assigned to and seated with a team, with whom you will attempt to solve as many challenges as you can within today’s hack time

You have been assigned a coach who will be your first point of contact, and is here to support you and answer questions during the hack. They will not however solve the challenges for you.

The Premise
You are the R&D team for a startup that wants to investigate the technical viability of renting out Minecraft server instances online. The business model is already well established in the market. Customers pay a fixed fee for access to an online multiplayer Minecraft server, or even a dedicated server of their own.

The challenges
Each challenge will lead you through a stage of the technical investigation as briefly laid out by your fictional CTO, these investigations become more technically challenging as you progress.

We do not provide guides, or instructions to solve the challenges, just a few hints and documentation references that you may find useful. There are multiple ways to solve each challenge, and very likely some we haven’t thought of, so whilst we have automated validation of the most common solutions, we’re interested to see your own unique solutions to the problem, and you should absolutely work with your coaches and the OpenHack Team to validate your solution as correct.

?

One final tip: Read everything very carefully
The Openhack team have worked hard to ensure each problem is solvable, all the details you should need are within the challenge briefs, which are very carefully written and worded, to give you clues toward the solution, reading them fully is the best way to figure out a solution, and small points can be easily missed. Your coaches will help to fill gaps in your understanding, provided you ask them the right questions of course.

Reference
The following information aims to give you a good understanding of Minecraft you may find helpful while working through the challenges.

1. Minecraft Servers
This OpenHack focuses on deploying older ‘Java’ Minecraft servers, we’ve taken the liberty of providing a custom container for these.

2. Minecraft Client & License
In order to test connections to your Minecraft servers, you’ll need the Minecraft Java Edition client for PC/Mac, and a Minecraft Account. You can find the download at Minecraft.net/Download

You’ll need to Create an Account before you login. You should find a ‘secret code’ on your Event Details sheet provided to you, this is a Minecraft License code and give you a full, permanent minecraft license for you to keep after the event.

3. Protocol: TCP
The Minecraft server accepts connections via TCP, not HTTP/REST

4. Container Images & Ports
Find the Minecraft images on Docker Hub
There are two ports of interest:
Default Minecraft port: 25565
Default RCON port: 25575
5. State
Minecraft server state is handled by a write-only operation to disk in the default ‘data’ directory. The server does not do any verification on the state. This means that if two Minecraft servers share that same default directory, they will overwrite each-others data.

6. Helpful 3rd Party Tools
If you need to get information from a Minecraft server, these tools are usually the first stop for most common requirements:

Minecraft Server Status Checker: Quickly check to see if your server is up and running.
Minecraft Status Info API: Get simple information about the status of your server.
mcstatus: The underlying code for the Minecraft Server Status Checker.
7. Server Commands
Here are the descriptions of some server commands you may encounter.

Command	Description
ban <playername> [reason]	Blacklists the name playername from the server so that they can no longer connect
banlist	Displays the banlist. To display banned IP addresses, use the command banlist ips
list	Shows the names of all currently-connected players
stop	Gracefully shuts down the server


-----------------------------------------------------------------------------------------------------------------------


Challenge 2 - Let’s get ready to cluster!
Background
Containers are extremely useful on their own, but their flexibility and potential is multiplied when deployed to an orchestrator/cluster.

You can learn more about the value of orchestrators at docs.microsoft.com, or more specifically the following links:

Service Fabric and Containers
Introduction to Azure Container Service (AKS)
Once you have a cluster configured, you can react quickly to demand and leverage the extended functionality of the underlying platform to best suit your needs whether that’s:

Deploying quickly and reliably.
Scaling on at will to meet demand.
Rolling out new features or upgrades.
Utilizing only what resources you need for your current provision.
Challenge
Your team’s goal in this challenge is to deploy the same container you used in challenge 1, to a cluster in Azure either with Service Fabric or Azure Container Service (AKS) in the EastUS Azure region.

Success Criteria
Create a cluster in Azure, running v1.0 of your chosen container, in the EastUS Azure region.
References
You can find the Minecraft containers on Docker Hub
HINT: There is a second port on a Minecraft server for RCON (Remote Console) 25575, in addition to the default connection port (25565). The hack portal uses this to verify your server!!
Some other useful resources in addition to the ones in challenge 1 are:

Azure resource naming best practices
Azure CLI reference
Kubectl overview
Service Fabric Containers Overview

------------------------------------------------------------------------------------------------------------------------


Challenge 3 - I’m in the cloud, now what?
Background
Creating a cluster is just the first step for running containers in production, and it’s important to think about operations and scenarios around your deployments. Areas such as telemetry and monitoring of clusters and services, state persistence, upgrades, and scale should all be planned for, and each come with their own unique challenges.

When taking your own existing/legacy applications and deploying them into the cloud, you want to have confidence that they’re reliable, available, and tolerant to failures.

Challenge
Your team’s goal in this challenge is to get your hands dirty with some real-world concerns by modifying the cluster and application you deployed in challenge 2. Your team will make that deployment ‘production ready’ by adding telemetry and monitoring, performing a successful container upgrade, adding reliable state to your container deployments, and scaling your cluster to multiple instances.

A Note on Containers and Minecraft State
State in containers by default is local to the container. As such, when the container is reset or recreated, the state goes with it forever. This is fine if your service or application is stateless, or only needs state during runtime, but not so much with persistent online worlds such as Minecraft. The typical solution? Connect the container to storage that exists outside of it, so that the state lives on long after any individual container is gone.

Minecraft server state is handled by a write-only operation to disk. The state is not verified by the server. This means that if two Minecraft servers share the same ‘data’ directory and use the default filename, they’ll overwrite each other’s data. No one will notice until the servers restart, leaving one server’s data intact, and the other’s lost forever (whichever server had an active player on it last, will win.)

One more thought: in the case where external storage is used and the servers are gone forever, how does that state get cleaned up?

Success Criteria
Implement a monitoring/telemetry solution for your cluster and demonstrate it to a coach. This should include:
Memory usage per instance and or node.
CPU usage by instance or node.
Version of the container running on any given instance.
Number of container instances running.
Population of each Minecraft server.
Total current players and available capacity.
Upgrade your instance within the cluster above, to v2.0

Configure your container deployments so that the state is persisted between instance resets. In order for us to verify this, you must ensure that both the default port for Minecraft (25565) and the default port for RCON (25575) must be exposed and available publicly.

References
You can find the Minecraft containers on Docker Hub
HINT: Just a reminder the RCON port for minecraft is 25575, in addition to the default connection port (25565)
HINT: Players expect that if they connect to the same server, the world is just as they left it and everything they built is still there (unless another player destroyed it).
Some other useful resources in addition to the ones in challenge 2 are:

Azure resource naming best practices
Azure CLI reference
Kubernetes volumes
Service Fabric Containers Overview


------------------------------------------------------------------------------------------------------------------------


Challenge 4 - Welcome to management
Background
As mentioned in Challenge 3, a cluster allows you to minimize the friction that often comes with deployments, scaling, and upgrades. However, initiating these processes manually via kubectl commands or Service Fabric PowerShell scripts can begin to feel just as tedious. Your players also have no way to lookup your servers.

Challenge
Your team’s goal in this challenge is to create a REST API to: list, add and delete instances from your cluster and a web application which provides an intuitive visual interface on top of your API for administrators to manage your cluster and players to lookup available servers.

You should also ensure that your telemetry and monitoring solution is kept up to date, and is integrated into web application in a useful way.

The REST API for listing servers should return a JSON array of tenants with the following properties. You may include additional properties in the response as desired, but they will be ignored by the automated verification:

[
  {
    "name": "<some arbitrary name>",
    "endpoints": {
      "minecraft": "<publicly available IP:port>",
      "rcon": "<publicly available IP:port>"
    }
  }
]
An example response might look like:

[
  {
    "name": "tenant1",
    "endpoints": {
      "minecraft": "128.124.90.15:25565",
      "rcon": "128.124.90.15:25575"
    }
  },
  {
    "name": "tenant2",
    "endpoints": {
      "minecraft": "128.194.90.16:25565",
      "rcon": "128.194.90.16:25575"
    }
  },
  {
    "name": "tenant3",
    "endpoints": {
      "minecraft": "128.194.90.16:25566",
      "rcon": "128.194.90.16:25576"
    }
  }
]
Success Criteria
Your REST API should be able to add and remove instances from your cluster.

Your REST API should return a list of servers that meets the above specification.

Your web application should allow players to view a list of available servers, with relevant information to allow them to connect directly.

Your web application should have an administrative interface that the cluster and instances can be managed from.

Submit an endpoint for your REST API server-list to the OpenHack portal. The portal will verify that the response meets our specification and that connections can be made to the returned endpoints, Ensure you have scaled your cluster so that your API returns more than 1 instance.

Demonstrate your web application to a coach, and be sure to point out the management functionality, and telemetry and monitoring options you have included.

References
Hint: there are no points for style, but it helps!
docs.microsoft.com Is a great place to start considering options for your solution here.
Azure Functions offer an alternative approach for some functionality that may be useful in this or future challenges.



----------------------------------------------------------------------------------------------------------------------



Challenge 5a - Going global
Background
Larger scale applications often aren’t restricted to just one geographical location. It’s often the case that improvements in performance through reduction of latency are leveraged by larger services to offer customers the very best experience possible. Containers allow you to quickly deploy identical versions of a service to different geographies, perform rolling ‘follow the moon’ upgrades to minimize end-user downtime, and even to fail-over compute. As powerful and flexible as cluster technology now is, there’s a limit to how much is configured automatically, and that’s where you come in…

Challenge
Your challenge is to replicate the cluster you created in challenge 2, by creating a second cluster across the Atlantic ocean in the WestEurope region. You’ll still only be able to use one endpoint for verification, but any traffic that hits your cluster should be routed to the nearest geographical cluster. You should also be able to monitor and manage both of your clusters using the tenant manager service and portal you created in challenge 4.

Success Criteria
Given the complexity and variety of potential solutions, the goals for this challenge are verified by our team of expert coaches.

One cluster present in EastUS region.
One cluster present in WestEurope region.
Traffic to endpoint is routed to nearest available cluster.
If a cluster is entirely unavailable (offline), traffic should be routed to the next available cluster.
Monitoring, Telemetry and Tenant Management available for both clusters.
References
Azure Traffic Manager Overview
Azure Load Balancer Overview