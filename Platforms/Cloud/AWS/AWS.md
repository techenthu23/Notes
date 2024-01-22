

AWS

It's able to execute really well.

And it has a really great completeness of vision.

It is followed closely by Microsoft and Google.

AWS is a Global Infrastructure and has
• AWS Regions 
• AWS Availability Zones 
• AWS Data Centers 
• AWS Edge Locations / Points of Presence
• And all of these can be represented on the map right here https://infrastructure.aws/


## AWS Regions

AWS Regions are separate geographic areas that AWS uses to house its infrastructure. T

__Best practices for choosing AWS Region__

In general, try to follow these best practices when you choose a region, to help ensure top performance and resilience:
- __Proximity__: Choose a region closest to your location and your customers’ location to optimize network latency.
- __Services__: Try and think about what are your most needed services. Usually, the newest services start on a few main regions then pop up in other regions later.
- __Cost__: Certain regions will cost more than others, so use built-in AWS calculators to do rough cost estimates to inform your choices.
- __Service Level Agreement (SLA)__: Just as with cost, your SLA details will vary by region, so be sure to be aware of what your needs are and if they’re being met.
- __Compliance__: You may need to meet regulatory compliance needs such as GDPR by hosting your deployment in a specific — or multiple regions.

_What AWS Regions have the most services?_

Not all regions are created equally. These regions have more services than others in their general areas:
- Americas: US East (N. Virginia), US West (N. California)
- Asia Pacific:  Singapore, Sydney, Tokyo
- EU: Frankfurt, Ireland

## AWS Availability Zone (AZ)

An AWS Availability Zone (AZ) is the logical building block that makes up an AWS Region. There are currently 69 AZs, which are isolated locations— data centers — within a region. Each region has multiple AZs and when you design your infrastructure to have backups of data in other AZs you are building a very efficient model of resiliency, i.e. a core concept of cloud computing.



AWS has the concept of a Region, which is a physical location around the world where we cluster data centers. We call each group of logical data centers an Availability Zone. Each AWS Region consists of a minimum of three, isolated, and physically separate AZs within a geographic area. Unlike other cloud providers, who often define a region as a single data center, the multiple AZ design of every AWS Region offers advantages for customers. Each AZ has independent power, cooling, and physical security and is connected via redundant, ultra-low-latency networks. AWS customers focused on high availability can design their applications to run in multiple AZs to achieve even greater fault-tolerance. AWS infrastructure Regions meet the highest levels of security, compliance, and data protection.

An Availability Zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity in an AWS Region. AZs give customers the ability to operate production applications and databases that are more highly available, fault tolerant, and scalable than would be possible from a single data center. All AZs in an AWS Region are interconnected with high-bandwidth, low-latency networking, over fully redundant, dedicated metro fiber providing high-throughput, low-latency networking between AZs. All traffic between AZs is encrypted. The network performance is sufficient to accomplish synchronous replication between AZs. AZs make partitioning applications for high availability easy. If an application is partitioned across AZs, companies are better isolated and protected from issues such as power outages, lightning strikes, tornadoes, earthquakes, and more. AZs are physically separated by a meaningful distance, many kilometers, from any other AZ, although all are within 100 km (60 miles) of each other.

## AWS Local Zones

AWS Local Zones place compute, storage, database, and other select AWS services closer to end-users. With AWS Local Zones, you can easily run highly-demanding applications that require single-digit millisecond latencies to your end-users such as media & entertainment content creation, real-time gaming, reservoir simulations, electronic design automation, and machine learning.

Each AWS Local Zone location is an extension of an AWS Region where you can run your latency sensitive applications using AWS services such as Amazon Elastic Compute Cloud, Amazon Virtual Private Cloud, Amazon Elastic Block Store, Amazon File Storage, and Amazon Elastic Load Balancing in geographic proximity to end-users. AWS Local Zones provide a high-bandwidth, secure connection between local workloads and those running in the AWS Region, allowing you to seamlessly connect to the full range of in-region services through the same APIs and tool sets.

# AWS Wavelength

AWS Wavelength enables developers to build applications that deliver single-digit millisecond latencies to mobile devices and end-users. AWS developers can deploy their applications to Wavelength Zones, AWS infrastructure deployments that embed AWS compute and storage services within the telecommunications providers’ datacenters at the edge of the 5G networks, and seamlessly access the breadth of AWS services in the region. This enables developers to deliver applications that require single-digit millisecond latencies such as game and live video streaming, machine learning inference at the edge, and augmented and virtual reality (AR/VR). AWS Wavelength brings AWS services to the edge of the 5G network, minimizing the latency to connect to an application from a mobile device. Application traffic can reach application servers running in Wavelength Zones without leaving the mobile provider’s network. This reduces the extra network hops to the Internet that can result in latencies of more than 100 milliseconds, preventing customers from taking full advantage of the bandwidth and latency advancements of 5G.


## AWS Outposts

AWS Outposts bring native AWS services, infrastructure, and operating models to virtually any data center, co-location space, or on-premises facility. You can use the same AWS APIs, tools, and infrastructure across on-premises and the AWS cloud to deliver a truly consistent hybrid experience. AWS Outposts is designed for connected environments and can be used to support workloads that need to remain on-premises due to low latency or local data processing needs.


## Accessing regions and availability zones via endpoints

In a nutshell, endpoints are specific URLs that act as an entry point for a web service, and many AWS services will have an endpoint based on its region/AZ. Endpoints aim to reduce further the latency of your applications, but not all AWS services support endpoints with regions/AZ data in the naming convention. Certain global services such as IAM will have a single endpoint: iam.amazonaws.com, but a good way to think about it is that most services that are related to a resource (such as an EC2 instance or a DB) will have an endpoint that references its geographic location, such as dynamodb.eu-west-1.amazonaws.com.