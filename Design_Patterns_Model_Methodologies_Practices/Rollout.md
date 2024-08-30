
In any software deployment, you should first deploy the software to a representative sample of systems and validate it before you deploy it more broadly. In many organizations, installation or operations teams deploy Data Center Security: Server Advanced
agents, while security or compliance functions maintain the detection policies and prevention policies. Plan the agent installation and deployment activities in phases to account for the differences in the workload and activities on production systems. By planning the agent installation and deployment activities in phases, you can proactively address any issues that you encounter.

Symantec recommends that you first deploy agents configured with the appropriate policies on a small number of representative systems, to ensure that the systems and any business applications operate properly. Then, from the Data Center Security: Server Advanced console, deploy policies to a representative pool of agents. Verify that the systems and business functions operate properly with the appropriate security policies applied.

See the Data Center Security: Server AdvancedAdministrator's Guide for information about applying policies to agents.

Incremental rollout is especially valuable for blocks of like systems such as a dozen domain controllers or many similar database, Web servers, or cluster members. Symantec considers it a best practice to deploy to a small subset of like systems first, say one to three agents, to validate that critical business applications function as desired. Incremental rollouts provide the opportunity to manage each checkpoint and not have to troubleshoot both installation issues and policy issues at the same time.
Incremental testing and incremental deployment are especially valuable for the small set of extreme systems that are found in some environments. These extreme systems exhibit high end scaling that is out of proportion in comparison to the rest of the enterprise. Such systems are candidates for additional scrutiny and testing before production deployment. Examples of extreme systems include the following:

    Systems that have more than 1,000 concurrent processes where most other systems may have less than 100 processes.
    Systems that monitor changes on 500,000 files where most other systems generally have less than 10,000 files to monitor.
    Domain controllers, database servers, or other application servers that handle an order of magnitude more requests than typical systems.
    Systems that are near the limit of CPU or memory usage before Data Center Security: Server Advanced
    is installed.
    A clustered environment