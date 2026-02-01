## **Microservices Deployment Patterns – Study Notes**

### **Deployment Forces**

These are the core factors that shape microservice deployment strategies:

* **Language & Framework Diversity**

  > Microservices allow each team to choose their preferred tools, resulting in a polyglot architecture (Java, Node.js, Python, etc.). The deployment platform must support diverse runtime environments.

* **Multiple Instances per Service**

  > To ensure scalability and availability, each service typically runs multiple instances.

* **Fast Build & Deployment**

  > Quick feedback loops enhance developer experience and enable rapid scaling or bug fixes in production.

* **Independent Deployment & Scaling**

  > Services must be independently deployable and scalable to avoid inter-service dependencies during releases.

* **Isolation of Service Instances**

  > One service should not be able to interfere with or degrade the performance of others (e.g., through memory leaks).

* **Resource Constraints**

  > Each service should be allocated bounded resources (memory, CPU, network bandwidth) to prevent resource contention.

* **Reliable and Cost-Effective Deployment**

  > The platform should minimize downtime and infrastructure cost.

---

### **Legacy Deployment Patterns**

#### **1. Multiple Service Instances per Host**

> Multiple services (e.g., WAR files) deployed on a shared application server or VM.

* ✅ **Benefits:**

  * Efficient resource use (e.g., one JVM hosts multiple WARs)
  * Fast and simple to deploy (especially in dev environments)

* ❌ **Drawbacks:**

  * Poor isolation – one service can affect others
  * Hard to monitor and debug
  * Difficult to enforce resource limits
  * Risk of version conflicts (e.g., Java 8 vs. Java 21)
  * Tight coupling to deployment stack and infrastructure

#### **2. Service per Virtual Machine**

> Each microservice is deployed on a separate VM (e.g., EC2).

* ✅ **Benefits:**

  * Strong isolation and fault tolerance
  * Each service encapsulates its own runtime and tech stack
  * Leverages cloud-native features like autoscaling and load balancing

* ❌ **Drawbacks:**

  * Heavy resource overhead (full OS + runtime per service)
  * Slower provisioning (minutes to spin up VMs)
  * More expensive compared to containers

---

### **Modern Deployment Patterns**

#### **3. Service as a Container**

> Each service is packaged and deployed as a container (e.g., using Docker).

* ✅ **Benefits:**

  * Lightweight isolation (shared OS kernel, but separated namespaces)
  * Technology encapsulation with consistent APIs for deployment (`docker run`, Kubernetes manifests)
  * Fast startup times compared to VMs
  * Better resource efficiency (lower memory/CPU footprint)
  * Easily reproducible builds with `Dockerfile`

* 🛠️ **Example: Health Check**

  ```dockerfile
  HEALTHCHECK --start-period=30s --interval=5s \
    CMD curl http://localhost:8080/actuator/health || exit 1
  ```

* ❌ **Drawbacks:**

  * Manual maintenance of container images (OS/runtime patching)
  * Limited fault tolerance with `docker run` alone
  * Requires orchestration for production-ready reliability
  * Infrastructure management burden unless using hosted platforms (e.g., AWS ECS, GKE)

* 🧰 **Better Approach: Docker Compose or Orchestration Tools**

  * **Docker Compose** is great for local dev but limited to one machine.
  * **Kubernetes** (or ECS) turns many machines into a single resource pool, offering:

    * Autoscaling
    * Rolling deployments
    * Service discovery
    * Self-healing (restarts crashed containers)

---

#### **4. Serverless Deployment Pattern**

> Services are deployed as functions in a managed environment (e.g., AWS Lambda, Google Cloud Functions).

* ✅ **Benefits:**

  * No server management – fully abstracted infrastructure
  * Pay-per-use model – cost-effective for bursty or low-throughput workloads
  * Automatic scaling and fault tolerance
  * Extremely fast deployment and rollback

* ❌ **Drawbacks:**

  * Limited control over the runtime environment
  * Cold start latency (especially in Java or .NET runtimes)
  * Harder to run complex long-lived workloads
  * Vendor lock-in concerns
  * Difficult to enforce some types of observability and debugging

---

### **Modern Deployment Realities**

With microservices and DevOps, deployment has become:

* **Developer-led**: The same team that writes the code owns deployment.
* **Immutable**: No SSH access; servers and containers are treated as **disposable** and are replaced rather than modified ("cattle, not pets").
* **Automated**: Manual server configuration is infeasible at scale. Deployment pipelines and infrastructure as code are mandatory.
* **Observable**: Monitoring, logging, and tracing are baked into the environment.

---

### **Four Key Capabilities for Modern Microservice Deployment**

1. **Service Management Interface**

   > A programmatic API (usually REST) for creating, updating, and configuring services. Enables CLI/GUI tools to integrate into CI/CD.

2. **Runtime Service Management**

   > Ensures desired number of instances are always running; replaces crashed services and rebalances load when machines fail.

3. **Monitoring / Observability**

   > Visibility into logs, metrics, and traces. Alerts developers to issues in real time. Tools include Prometheus, Grafana, ELK Stack, AWS CloudWatch.

4. **Request Routing**

   > Directs incoming user requests to the right service instances. Can be implemented via load balancers, service meshes (e.g., Istio), or API gateways.
