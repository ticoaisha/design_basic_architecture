# Developing a conceptual design for a cloud infrastructure supporting a web application

## Three-tier Architecture Overview

Before the designing a cloud infractucture, it is necessary to decide how many tiers your infrastructure will have. For the purposes of this project, I have decided to go with the three tier cloud architecture, which includes: presentation (web) tier, application (logic), data (database) tier. 

Three-tier architecture is a well-known software architecture pattern used to organize applications, particularly web applications, into three separate layers or "tiers." This model divides the application into logical layers with specific roles, making it easier to develop, manage, and scale each tier independently. Here is a quick overview of each of the three layers in a three-tier architecture:

1. **Presentation (web) tier** is the user interface layer that interacts directly with the user. It displays information and collects user input. Typically includes a web server, browsers, mobile apps, or any client that users interact with. For example, front-end tools, such, as HTML, CSS, JavaScript, front-end frameworks, such as Angular, React, or any mobile app front ends.
*Purpose:* It communicates with the application tier to send user requests and receive processed data for display.
2. **Application (logic) tier** is responsible fot the business logic of the application: processesing of the user requests, making logical decisions, computations. This tier is represented by application servers, backend logic, and frameworks like Spring Boot, Node.js, Django, Ruby on Rails, etc.
*Purpose:* It acts as a bridge between the presentation and data tiers. The application tier retrieves and processes data from the data tier and sends the results back to the web tier.
3. **Data (database) tier** stores and manages data for the application. It is responsible for data storage, retrieval, and persistence. This tier is represented by the databases, such as MySQL, PostgreSQL, MongoDB, Oracle, and data storage solutions.
*Purpose:* It receives queries from the application tier, performs the requested operations (read, update, delete), and returns the data to the application tier.

Let's see the logic of three tiers working together. The user interacts with the presentation tier by accesing a web page. The request is sent to the application tier, which applies the necessary logic (e.g., processes a form submission, retrieves data, performs calculations).
The application tier communicates with the data tier to fetch or update data as needed. The processed data is then sent back to the web tier for display to the user.

## Benefits of Three-tier Architecture:

The main benefits of the three-tier architecture include:

* **Scalability:** Each tier can be scaled independently based on demand.
* **Separation of Concerns:** Each tier focuses on a specific role / purpose, making it easier to develop, maintain, and test.
* **Flexibility:** Different technologies can be used for each tier, and they can be upgraded or replaced independently.
* **Security:** Sensitive data and logic can be kept secure by limiting direct access to the data tier.

As we can see from the mentioned above, this architecture is commonly used for web applications because it ensures a clear separation between user interface, business logic, and data management, promoting a more modular and maintainable approach to the application development.

## Architectural Diagram

In order to understand the logic of the three-tier architecture, we need to create and describe architectural diagram. This approach will help not only to visualize the concept but also to understand the logic flow between different tiers and cloud services.
Representation of the three-tier cloud architecture using cloud services and resources is shown below with the description of each of the components followed. This diagram was drawn with help of [draw.io](https://app.diagrams.net/).

[Architectural Diagram](/design_basic_architecture/1_architectural_design_three_tier.png)

Before approaching sacling decisions for each of the components, let's fist understand logical flow of this architecture.
Users initiate requests through the Internet. Amazon Route 53 provides DNS resolution for incoming requests and routes traffic to Amazon CloudFront (assuming CloudFront is configured) for content delivery and caching. Requests flow from CloudFront or directly to the Application Load Balancer (ALB). Application Load Balancer resides in a public subnet and distributes incoming traffic accross multiple public EC2 instances in an Auto Scaling Group (ASG), providing high availability and load balancing. The public EC2 instances of the presentation tier receive requests and pass them securely to the private EC2 instances of the application tier located in the private subnets. Private EC2 instances process application logic and communicate with the primary database and read replica located in the private subnets of the database tier for the data operations. NAT Gateways in the public subnets allow private EC2 instances to access the internet for the updates and outbound connections without exposing them directly to the inbound internet traffic. Security groups and Network Access Control Lists (NACL) are configured to control and secure traffic flow between all the components of this architecture, ensuring a robust security model.
Obviously another components could be added to this architecture but for the simplicity and puroses of this deliverable, let's keep it this way. And, of course, purpose and features of each of the components depicted could be learned in more details in order to understand their capabilities.

## Scaling Decisions

Now let's talk about the scaling decisions for each of the components.

1. ALB -> horizontal scaling that is automatically managed by AWS. This allows the ALB to distribute incoming traffic across multiple targets (e.g., EC2 instances), which, in turn, ensures high availability, fault tolerance, and the ability to handle fluctuations in traffic effectively.

2. Public EC2 Instances -> horizontal scaling, which allows to add or remove instances based on demand. This approach ensures that the system can handle varying traffic loads while maintaining performance and resilience, and it is cost-effective compared to scaling a single instance vertically.

3. Private EC2 Instances -> horizontal scaling, that is used here to ensure high availability and load distribution across multiple instances, allowing the system to handle increased demand efficiently. This also improves fault tolerance, as traffic can be routed to other instances if one fails.

4. NAT Gateway -> horizontal scaling as needed, that is managed automatically by AWS, to handle outbound internet traffic from instances in private subnets. This ensures secure outbound connectivity while blocking inbound traffic, without manual scaling intervention.

5. Primary Database -> vertical scaling to improve write operations, which is achieved by adding more resources such as CPU and memory.

6. Read Replica -> horizontal scaling for read operations allows distributing read requests accross multiple instances, reducing the load on the primary database, enhancing read performance and fault tolerance, and improving availability.

## Purpose of Each of the Architectural Components

Each of the components in the above diagram serves its particular role allowing logical flow to happen. 

* ALB -> distributes incoming traffic among multiple targets (e.g., EC2 instances) to ensure high availability, scalability, and reliability of the application.

* Public EC2 Instances -> provide the user-facing interface, handle incoming requests from users, process front-end application logic, and serve static and dynamic content. 

* Private EC2 Instances -> process the core business logic, interact with databases, and handle secure, internal communications and data processing.

* NAT Gateway -> allows instances in private subnets to initiate outbound connections (e.g., downloading updates, accessing APIs) while preventing inbound internet connections for enhanced security.

* Primary Database -> stores critical application data, handles write operations, and ensures data consistency across the application.

* Read Replica -> offloads read operations from the primary database, improving read performance and providing redundancy for increased availability.

## Reasons Behind Architectural Decisions

In addition to having specific reasons to choose three-tier architecture (described above), let's point out the reasons for some internal architectural decisions, specifically, regarding the horizontal scaling, vertical scaling, and NAT Gateway.

* Horizontal scaling for ALB and EC2 Instances is cost-effective and enhances system resilience and fault tolerance by distributing the load across multiple instances. It provides the flexibility to automatically adjust capacity based on demand, ensuring that traffic spikes do not overwhelm any single resource.

* Vertical scaling for the primary database ensures high write performance. Horizontal scaling for read replicas ensures improvement of the performance of the read operations, offloading work from the primary database and improving overall system responsiveness.

* Placing a NAT Gateway in the public subnet allows private instances to securely access the internet for updates or API calls while keeping them protected from inbound internet traffic.

To summarize, this cloud infrastructure with the help of internal components and described architectural decisions prioritizes scalability, performance, fault tolerance, and security. By practicing how to create architectural diagram, any cloud professional has a great opportunity to upskill in ability to present the cloud infrastructure, select right services and components, understand purpose of each and interactions between them, as well as interpret reasons behind particular architectural decision.






