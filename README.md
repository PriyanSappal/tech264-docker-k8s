# Docker and Kubernetes

[Docker](Docker/README.md)

---

[Kubernetes](Kubernetes/README.md)

---

# Upcoming Assessment Topics
## What are the implecations of a pod being ephemeral?
* persistent volumess can solve the issue of losing data.
 
## What is a maintained image and why should we use them?
* It's a container image originated from trusted sources, like docker.
* They have regular updates, better security (receive regular security patches and updates).
 
## What's the difference between DevOps, SRE and platform Engineering?
**DevOps**
* Focuses on collaboration between development and operations.
* Delivering code quicker to end users.
* Help the developers to be able to deliver their code faster for the end users so it can create value for the business.
  * Make a developers life easier.
* Provide consistent environments for the developers to use.
 
**SRE** (Site Reliability Engineering)
* Focuses on reliability and uptime of services: aka, anything they need to do to keep things up and running.
* Makes sure that the changes that are applied don't break the infrastructure so the application is always available.
  * They want the highest up-time possible.
 
**Platform Engineering**
* Building and maintaining internal platforms.
* Focuses on creating and managing the infrastructure that supports development teams.
* Developing a platform/ecosystem where developers can access the tools and resources they need to perform their tasks efficiently.
 
<br>
 
### Table of Differences
 
| **Aspect**          | **DevOps**                                      | **SRE (Site Reliability Engineering)**                  | **Platform Engineering**                              |
|---------------------|-------------------------------------------------|--------------------------------------------------------|------------------------------------------------------|
| **Focus**           | Collaboration between development and operations | Reliability and uptime of services                      | Building and maintaining internal platforms           |
| **Key Activities**  | CI/CD, automation, monitoring, and collaboration | Monitoring, incident response, and automation           | Creating tools and infrastructure for developers      |
| **Goal**            | Faster and more reliable software delivery       | Ensuring services are reliable and scalable             | Improving developer productivity and efficiency       |
| **Approach**        | Cultural shift and practices                     | Applying software engineering to operations problems    | Providing reusable components and services            |
 
<br>
 
## How Secure are Kubernetes Secrets?
* It's encoded, not encrypted.
* Anyone with access to the namespace, where those secrets are stored, can access those secrets.
 
What would be better for production?
* Avoid storing sensitive info in certain places by assigning particular roles.
* Azure Key-Vault, KMS (AWS): Manage secrets with a key vault.
 
## What are the biggest challenges facing enterprises?
* Vendor lock-in.
  * Enterprises often become dependent on a single vendor for products and services, making it difficult and costly to switch providers.
  * Organisations can get stuck with their data and how its structure because theyve chosen to use a managed service.
  * Vendors will store this data in a particular format, if they wat to change this, it's a huge challenge because it's stored in a particular way.
* Cost.
* Keeping Data Secure.
  * They may want to move their data to the cloud, but they're worried about keeping it secure.
  * They want to physically controll access to where their hardware is stored.
  * They may not want to migrate the entire application onto the cloud because they may want to keep parts of those applications on-prem.
    * This results in a **hybrid** cloud solution.
    * More complicated and requires more expertise.
 
## How do these organisations handle Kubernetes?
* If they want to use Kubernetes, they have to abstract (seperate) the complexities of Kubernets and match them with certain developers.
* Challenges: higher complexities, more expertise, dedicated kubernetes team.
 
## Why/when would a business NOT want to use a microservices architecture?
* slow/low-runtime.
* Small, simple application: fine to use a monolith.
* Early stages of development.
* A business is not ready with having expertise available to handle the complexity of a microservices architecture.
* Some organisations have a cult culture to develop monoliths.
* An application that needs to use every part of the database.
  * The challenge of breaking this aplpication up might not be worth the change. Too big a challenge.
 
The main reason that makes one architecture more successful than the other: the culture of the organisation.