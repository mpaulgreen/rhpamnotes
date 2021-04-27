- OpenShift Container Platform
    - Container orchestration platform based on Kubernetes
    - Benefits both operations and development
    - Provides developers and IT organizations with cloud application platform
        - Used for deploying applications on secure, scalable resources
        - Minimal configuration and management overhead
    - Supports Java™, Python, Ruby, Node.js, Perl, PHP, .NET, and more
    - OpenShift Container Platform’s Control Plane is only available to be deployed on RHCOS
    - OpenShift Container Platform workloads may be deployed on RHCOS or Red Hat Enterprise Linux
        - RHCOS available only for OpenShift deployments, not for general use
        - RHCOS codifies operational expertise for OpenShift with new purpose-built tooling
        - RHCOS is FIPS-compliant
    - Brings Kubernetes platform to customer data centers and cloud
        - Meets security, privacy, compliance, and governance requirements

- Product and Infrastructure Providers
    - OpenShift 4 is truely a hybrid cloud solution
    - Can be deployed fully automatically on public cloud, private cloud or automated infrastructure
    - OpenShift is capable of automating infrastructure deployment and product deployment. This is also known as installer provisioned infrastructure (IPI).
    - IPI is supported on AWS, Azure, GCP, Red Hat Open Stack, Red Hat Virtualization
    - Open Shift can be also installed on user provisioned infrastructure (UPI), that has networking, storage and compute. (AWS, Azure, GCP, Red Hat Virtualization, Red Hat Open Stack, VMWare vSphere, Bare Metal)
    - OpenShift 3.11
        - Supported anywhere RHEL runs
                - Bare-metal physical machines, virtualized infrastructure, in private or certified public clouds
                - Virtualization platforms: Red Hat Virtualization, vSphere, Hyper-V
                - Red Hat OpenStack Platform, certified public cloud providers like Amazon, Google, Azure
                - x86 and IBM Power server architectures
    - OpenShift Kubernetes Engine
        - Users explore OpenShift 4 kuernetes engine, not entire platform
        - Core K8s functionality with big ISV ecosystem
        - Enjoy RHCOS immutable and secure architecture
        - Appeals to DIY. *KS or lower end
- Architecture and Concepts
    - OpenShift runs on RHCOS and RHEL
    - OpenShift has two types of nodes
        - Workers
        - Masters
    - Nodes are 
        - Instances of RHEL or RHCOS with OPenShift Installed
        - Workers: Where end user application runs
        - Masters - Manage the cluster. It makes up the OpenShift control plane.
        - The OpenShift node daemons and other software run on all nodes.
    - Containers
        - All application instances and components run in OCI-compliant containers
        - OpenShift worker node can run many containers
        - Node capacity related to memory and CPU capabilities of underlying resources
    - POD
        - One or more containers deployed together on one host
            -  Consists of colocated group of containers with shared resources such as volumes and IP addresses
            - Smallest compute unit defined, deployed, managed
        - May contain one or more tightly coupled, colocated applications, ones that run with shared context
        - Orchestrated unit in OpenShift
            - OpenShift schedules and runs all containers in pod on same node
            - Complex applications made up of many pods, each with own containers
                - Interact externally and also with one another inside OpenShift environment
            - OpenShift runs container images in containers wrapped by meta object called "pod"
            - Possible to have multiple containers in single pod
                - Example: To support cluster features as sidecar containers
            - Most applications benefit from flexibility of single-container pod
                - Different components such as application server and database generally not placed in single pod
                - Allows for individual application components to be easily scaled horizontally
            - Application components are wired together by services
    - Service 
        - Defines logical set of pods and access policy
            - Provides permanent internal IP address and host name for other applications to use as pods are created and destroyed
        - Service layer connects application components together
            - Example: Front-end web service connects to database instance by communicating with its service
        - Services allow simple internal load balancing across application components
            - OpenShift automatically injects service information into running containers for ease of discovery
    - Labels 
        - Used to organize, group, or select API objects
            - Example: Tag pods with labels, services use label selectors to identify pods they proxy to
            - Makes it possible for services to reference groups of pods
            - Able to treat pods with potentially different containers as related entities
        - Most objects able to include labels in metadata
        - Group arbitrarily related objects with labels
        - Labels are simple key-value pairs:
    - Master Nodes
        - Instances of RHCOS
        - Primary functions:
            - Orchestrate activities on worker nodes
            - Know and maintain state within OpenShift environment
        - Three masters for high availability
    - etcd
        - Desired and current state held in data store that uses etcd as distributed key-value store
        - etcd also holds:
            - RBAC rules
            - Application environment information
            - Non-application user data
    - Master Services - Core Kubernetes Components
        - Kubernetes API server
        - Scheduler
        - Cluster Management Services ( Replication , Metering or Monitoring)
    - Master Services - Core OpenShift Components
        - OpenShift features core OpenShift components
        - OpenShift API server
            - API calls unique to OpenShift
            - Operator Lifecycle Manager (OLM)
            - Over-the-air updates of Operators for workloads, middleware, etc.
            - Web console
    - Master Infrastructure Services
        - Masters and workers collaborate
        - Both OpenShift and Kubernetes services included
        - Run as pods; orchestrated by master control plane
        - Services include:
            - Monitoring
            - Logging
            - OS tuning
            - Software defined networking (SDN)
            - DNS
            - Kubelet (OpenShift node process)
    - Master - API and Authentication
        - Masters provide single API endpoint that all tooling and systems interact with
            - Includes OpenShift and Kubernetes
            - All administration requests go through this API
        - All API requests SSL-encrypted and authenticated
        - Authorizations handled via fine-grained role-based access control (RBAC)
        - Masters able to be tied into external identity management systems, Examples - LDAP, Active Directory, OAUth providers like Github and Google
    - Access
        - All users access OpenShift through same standard interfaces
        - Web UI, CLI, IDEs all go through authenticated, RBAC-controlled API
        - Users do not need system-level access to OpenShift nodes
        - CI/CD systems integrate with OpenShift through these interfaces
        - OpenShift deployed on RHCOS enables use of next-generation systems management and monitoring tools
        - OpenShift deployed on RHEL enables use of existing systems management and monitoring tools
    - Health and Scaling
        - Masters monitor health of pods and automatically scale them
            - User configures pod probes for liveness and readiness
            - Pods may be configured to automatically scale based on CPU utilization metrics
            - Masters automatically restart pods that fail probes or exit due to container crash
            - Pods that fail too often marked as bad and temporarily not restarted
            - Service layer sends traffic only to healthy pods
                - Masters automatically orchestrate this to maintain component availability
    - Scheduler
        - Component responsible for determining pod placement
        - Accounts for current memory, CPU, and other environment utilization when placing pods on worker nodes
        - For application high availability, spreads pod replicas between worker nodes
        - Able to use real-world topology for OpenShift deployment (regions, zones, etc.)
        - Handles complex scenarios for scheduling workloads
        - Uses configuration in combination with node groups and labels
            - Example: Use regions and zones to carve up OpenShift environment to look like real-world topology
        - OpenShift 4 can be configured to schedule more infrastructure, automatically
    - Integrated Container Registry
        - OpenShift Container Platform includes integrated container registry to store and manage container images
        - When new image pushed to registry, registry notifies OpenShift and passes along image information including:
            - Namespace
            - Name
            - Image metadata
        - Various OpenShift components react to new image by creating new builds and deployments
    - Application Data 
        - Containers natively ephemeral
            - Data not saved when containers restarted or created
        - OpenShift provides persistent storage subsystem that automatically connects real-world storage to correct pods
            - Allows use of stateful applications
        - OpenShift Container Platform provides wide array of persistent storage types including:
            - Raw devices: iSCSI, Fibre Channel
            - Enterprise storage: NFS
            - Cloud-type options: Ceph®, AWS EBS, pDisk
    - Routing Layer
        - Provides external clients access to applications running inside OpenShift
        - Close partner to service layer
        - Runs in pods inside OpenShift
        - Provides
            - Automated load balancing to pods for external clients
            - Load balancing and auto-routing around unhealthy pods
        - Routing layer pluggable and extensible
            - Options include non-OpenShift software routers
    - ReplicaSet and Replication Controller
        - Two implementations:
            - ReplicaSet = Kubernetes
            - Replication controller = OpenShift
        - Ensures specified number of pod replicas running at all times
        - If pods exit or are deleted, replication controller instantiates more
        - If more pods running than needed, ReplicaSet deletes as many as necessary
    - Deployment and DeploymentConfig ( Cannot track auto scaled PODS)
        - Define how to roll out new versions of pods
        - Identify:
            - Image name
            - Number of replicas
            - Label target deployment nodes
        - Update pods based on:
            - Version
            - Strategy
            - Change Triggers
    - Kubernetes Operator is a Kubernetes native application
        - Puts all operational knowledge into Kubernetes primitives
        - Administrators, shell scripts, automation software (e.g. Ansible®) now in Kubernetes pods
        - Integrates natively with Kubernetes concepts and APIs
    - Operators: Design
        - Are pods with operator code that interact with Kubernetes API server
        - Run "reconciliation loops" to check on application service
        - Make sure user-specified state of objects is achieved
        - Manage all deployed resources and your application
        - Act as application-specific controllers
        - Extend Kubernetes API with Custom Resource Definition (CRD)
    - Operator Framework
        - Operator SDK
            - Developers build, package, test operator
            - No knowledge of Kubernetes API complexities required
        - Operator Lifecycle Manager (OLM)
            - Helps install, update, manage life cycle of all operators in cluster
        - Operator Metering
            - Usage reporting for Operators and resources within Kubernetes
    - OperatorHub.io
        - Kubernetes Internet community for sharing Operators
        - Works for any Kubernetes environment
        - Packages Operators for easy deployment and management
        - Publicizes Operators and enables adoption
        - Uses OLM to install, manage, update Operators
    - Networking Workflow
        - Container networking based on integrated Open vSwitch
        - Platform-wide routing tier
        - Ability to plug in third-party SDN solutions
        - Integrated with DNS and existing routing and load balancing
        - Route
            - Exposes service by giving it externally reachable host name
            - Consists of route name, service selector, and (optional) security configuration
            - Router can consume defined route and endpoints identified by service
            - Provides named connectivity
            - Lets external clients reach OpenShift-hosted applications
        - Router
            - Multi-tier applications easily deployed
                - Routing layer required to reach applications from outside OpenShift environment
            - Router container can run on any node host in environment
                - Administrator creates wildcard DNS entry (CNAME or A record) on DNS server
                - DNS entry resolves to node host hosting router container
            - Router is ingress point for traffic destined for OpenShift-hosted pods
                - Router container resolves external requests
                    (https://myapp.cloudapps.openshift.opentlc.com)
                    - Proxies requests to correct pods
            - Scenario
                - External client points browser to myApp.apps.openshift.opentlc.com:80
                - DNS resolves to host running router container
                - Using openshift-sdn overlay network:
                    - Router checks if route exists for request
                    - Proxies request to internal pod IP:port (10.1.2.3:8080)
            - POD Connectivity
                - Pods use network of OpenShift node host to connect to other pods and external networks
                - Scenario
                    - Pod transmits packet to pod in another node host in OpenShift environment
                        - Container sends packet to target pod using IP 10.1.2.3:8080
                        - OpenShift node uses Open vSwitch to route packet to OpenShift node hosting target container
                        - Receiving node routes packet to target container
            - Services and PODS
                - Services often used to provide permanent IP address to group of similar pods
                - Internally, when accessed, services load balance to appropriate backing pod
                - Backing pods can be added to or removed from service arbitrarily while service remains consistently available
                    - Enables anything that depends on service to refer to it at consistent internal address
                - Services have DNS names internal to OpenShift
                    - Example - my-service.my-project.svc.cluster.local
                    - PODS have access to internal DNS
                    - Scenario
                        - Pod transmits packet to service representing one or more pods
                            - Container sends packet to target service using IP 172.30.0.99:9999
                            - When service requested, OpenShift node proxies packet to pod represented by service (10.1.2.3:8080)
    - Container Deployment Workflow
        - New application requested via CLI, web console, or API
            - OpenShift API/authentication:
                - Approves request, considering user’s permissions, resource quotas, and other information
                - Creates supporting resources as needed: deployment configuration, replication controllers, services, routes, persistent storage claims
            - OpenShift scheduler:
                - Designates worker node for each pod, considering resources' availability and load, and application spread between nodes for application high availability
            - OpenShift worker node:
                - Pulls down image to be used from external or integrated registry
                - Starts container (pod) on worker node
- User Experience
    - Web Console
        - To perform administrative, management and trouble shooting tasks
        - It supports both adminsitrator and developer perspectives
        - Runs as pods on master node on openshift console project
        - It is managed by an operator POD
        - ISVs can customize the behavior of the Web console and direct customizations
        - Web console now features built in metrics reducing dependencies on external tool such as grafana
    - Developer perspective
        - Topology view
            - Application-centric
            - Shows components and status, routes, source code
            - Drag arrows to create relationships
            - Add components to applications easily
            - Project
                - Application Status, Resource Utilization, Project Event Stream, and Quota consumptions
                - Project Access
                    - Control users and groups
                - Troubleshoot the problems for an application using prometheus query language, or prompt UL
    - Administrator perspective
        - For project and cluster administrator
        - Cluster inventory - Status, Activity/Events, Capacity and Utilization
        - Every common resource type manageable
        - Logging and metrics
        - Advanced settings to view: Updates, Operators, CRDs, role bindings, resource quotas
    - Project
        - Allows groups of users or developers to work together
        - Unit of isolation and collaboration
        - Defines scope of resources
        - Allows project administrators and collaborators to manage resources
        - Restricts and tracks use of resources with quotas and limits
        - Kubernetes namespace with additional annotations
            - Central vehicle for managing resource access for regular users
            - Lets community of users organize and manage content in isolation from other communities
        - Users:
            - Receive access to projects from administrators
            - Have access to own projects if allowed to create them
        - Each project has own:
            - Objects: Pods, services, replication controllers, etc.
            - Policies: Rules that specify which users can or cannot perform actions on objects
            - Constraints: Quotas for objects that can be limited
            - Service accounts: Users that act automatically with access to project objects
        - Users and Projects
            - Interactions with OpenShift® always associated with user
                - System permissions granted by adding roles to users or groups
            - User types:
                - Regular Users
                    - How most interactive OpenShift users are represented
                    - Created automatically in system upon first login, or via API
                    - Represented with user object
                - System Users
                    - Many created automatically when infrastructure defined
                    - Let infrastructure interact with API securely
                    - Include: cluster administrator, per-node user, service accounts
            - Login and Authentication
                - Every user must authenticate to access OpenShift
                - API requests lacking valid authentication are authenticated as anonymous user
                - Policy determines what user is authorized to do
            - Web Console Authentication
                - Access web console at URL provided by your administrator
                - Provide login credentials to obtain token to make API calls
                - Use web console to navigate projects
            - Quotas and Limits
                - Resource Quotas
                    - OpenShift can limit:
                        - Number of objects created in project
                        - Amount of compute/memory/storage resources requested across objects in project
                        - Based on specified label
                            - Examples: To limit to department of developers or environment such as test
                    - Multiple teams can share single OpenShift cluster
                        - Each team in own project or projects
                        - Resource quotas prevent teams from depriving each other of cluster resources
                    - ResourceQuota object enumerates hard resource usage limits per project
                    - ClusterResourceQuota object enumerates hard resource usage limits for users across the cluster
                    - LimitRanges
                        - LimitRanges express CPU and memory requirements of pods' containers
                            - Set request and limit of CPU and memory particular pods' container may consume
                            - Aid OpenShift scheduler in assigning pods to nodes
                        - LimitRanges express quality of service tiers:
                            - Best Effort
                            - Burstable
                            - Guaranteed
                    - Default LimitRange for all pods/containers can be set for each project
                    - Compute Resources Managed by Quota Across Pods in Non-Terminal State
                        - cpu, requests.cpu - sum of CPU requests cannot exceed this value
                        - memory, requests.memory - Sum of memory requests cannot exceed this value
                        - limits.cpu - Sum of CPU limits cannnot exceed this value
                        - limits.memory - Sum of memory limits cannot exceed this value
                    - Object Counts Managed by Quota
                        - pods - Total number of pods in non-terminal state that can exist in project (pod is in terminal state if status.phase in (Failed, Succeeded) is true)
                        - replicationcontrollers - Total number of replication controllers that can exist in project
                        - resourcequotas - Total number of resource quotas that can exist in project
                        - services - Total number of services that can exist in project
                        - secrets - Total number of secrets that can exist in project
                        - configmaps - Total number of ConfigMap objects that can exist in project
                        - persistentvolumeclaims - Total number of persistent volume claims that can exist in project
                        - openshift.io/imagestreams - Total number of image streams that can exist in project
                    - Quota Enforcement
                        - After quota created in project:
                            - Project restricts ability to create resources that may violate quota constraint
                            - Usage statistics calculated every few seconds (configurable)
                        - If project modification exceeds quota:
                            - Server denies action
                            - Returns error message
                        - Error message includes:
                            - Quota constraint violated
                            - Current system usage statistics
                    - Cluster Monitoring Operator (CMO)
                        - Watches deployed monitoring components, resources; keeps up to date
                    - Prometheus Operator (PO)
                        - Manages Prometheus and Alertmanager
                        - Automatically generates monitoring targets based on Kubernetes label queries
                        - Alertmanager processes client alerts and routes to email, PagerDuty, etc.
                    - node-exporter: Agent to collect metrics
                    - kube-state-metrics: Converts objects to metrics
                    - Metrics Collection and Alerting with Prometheus - HPA
                        - Configure horizontal pod autoscaling (HPA) based on any metric from Prometheus
                            - Autoscale based on any cluster-level metrics from OpenShift
                            - Autoscale based on any application metrics
                        - Autoscales pods and machines
                            - Prometheus Adapter connects to single Prometheus instance (or via Kubernetes service)
                            - Manual deployment and configuration of adapter
                            - Cluster administrator needs to whitelist cluster metrics for HPA
        - Templates:
            - Can only create resources
            - Cannot manage or delete resources
            - Not associated with pod
        - Operators:
            - Create, manage, and delete resources
            - Implemented by operator pods
        - Helm 3:
            - Package of templates
            - How application packaged
            - How package installed
        - What Is a Template?
            - Describes set of objects that can be parameterized and processed to produce list of objects for OpenShift to create
            - Process templates to create anything you have permission to create within project
            - Can also define set of labels to apply to every object defined in template
        - What Are Templates For?
            - Create instantly deployable applications for developers or customers
            - Provide option to use preset variables or randomize values (like passwords)
        - Labels in Templates
            - Used to manage generated resources
            - Apply to every resource generated from template
            - Organize, group, or select objects and resources
            - Resources and pods are "tagged" with labels
            - Labels allow services and replication controllers to:
                - Determine pods they relate to
                - Reference groups of pods
                - Treat pods with different containers as similar entities
            - Parameters in Templates
                - Share configuration values between different objects in template
                - Values can be static or generated by template
                - Templates let you define parameters that take on values
                    - Value substituted wherever parameter is referenced
                    - Can define references in any text field in object definition
            - Creating Templates from Existing Objects
                - Can export existing objects from project in template form
                - Modify exported template by adding parameters and customizations
            - Operator Hub
                - Deploy and manage application in cluster with Operator
                - Discover Operators from Kubernetes community and Red Hat® partners
                - Install Operators on clusters to provide optional add-ons and shared services to developers
                - Capabilities provided by Operator appear in Developer Catalog, providing self-service experience
                - Add shared applications, services, or source-to-image builders to your project
                - Cluster administrators can install additional applications that show up automatically
            - Operator Interaction with OpenShift
                - Operators are pods that take advantage of custom resource definitions (CRDs)
                - CRDs allow extension of Kubernetes/OpenShift API
                    - API then knows new resources
                - CRDs allow creation of custom resources (CRs)
                - Operator watches for creation of CR, reacts by creating application
                - CRs managed in same way as stock OpenShift objects
                    - create, get, describe, delete, etc.
            - Operators - Custom Resource (Application) Creation
                - Custom Resources are simple definitions of resource you want Operator to create
                - Running Operator watches for CR to be created in either:
                    - Entire OpenShift cluster
                    - Project that Operator is running in
                - When CR created, operator receives event
                - Operator then creates all OpenShift resources that make up application
            - Operators - Custom Resource Management
                - To manipulate and examine CR, use oc commands
                - Do not scale ReplicaSets, Deployment, StatefulSets directly
                    - Use Operator to scale
                    - Operator continues to watch created resources and sets them back to initial states
                - To delete all created OpenShift API objects, delete CR
- Application Deployment
    - Current Technology - Evolving Technology
    - PaaS - Unopinionated container platforms
    - Complicated cluster deployments - Cluster managed self deployments
    - Business value interruptions for platform updates - Over the air updates
    - Custom configuration automation - Immutable infrastructure
    - IaaS - Cluster driven infrastructure
    - Simple application deployments - OpenShift service meshh drivben complex technologies
    - Microservices - OpenShift Serverless computing
    - Containers from internet - Validated, supported, runtime and middleware containers
    - Bespoke infrastructure deployment consulting - DevOps consulting and Red Hat Open Innovation labs
- Silos create inefficiency
    - Developers Care About:
        - Building applications
        - Automating tests
        - Continuous integration
        - Performance tuning
        - Debugging
    - Operations Cares About:
        - Deploying applications
        - Managing applications, infrastructure
        - Reliability
        - Security
        - Compliance
    - OpenShift deployments provide fine-grained management over applications
        - Based on user-defined template called "deployment configuration"
    - Features Provided by Deployment System
        - Deployment configuration: Template for running applications
            - Contains version number
                - Incremented each time new replication controller (Kubernetes ReplicaSet) created from configuration
            - Contains cause of last deployed replication controller
        - Triggers that drive automated deployments in response to events
        - Strategies to transition from previous version to new version
        - Rollbacks to previous version in case of deployment failure
            - Either manual or automatic
        - Manual replication scaling and autoscaling
    - Rollbacks
        - Deployments allow rollbacks
            - Rollbacks revert application back to previous revision
            - Performed via REST API, CLI, or web console
        - Deployment configurations support automatic rollback to last successful revision of configuration
            - Triggered if latest template fails to deploy
    - Deployment Strategies
        - Defined by deployment configuration
        - Determines deployment process
            - During deployments, each application has different requirements
                - Availability
                - Other considerations
            - OpenShift provides strategies to support variety of deployment scenarios
            - Readiness checks determine if new pod ready for use
                - If readiness check fails, deployment configuration retries until it times out
    - Rolling Deployment Strategy
        - Performs rolling update
        - Supports life-cycle hooks for injecting code into deployment process
        - Waits for pods to pass readiness check before scaling down old components
            - Does not allow pods that do not pass readiness check within timeout
        - Used by default if no strategy specified in deployment configuration
    - Rolling Deployment Strategy Process
        - Steps in rolling strategy process:
            - Execute pre life-cycle hook
            - Scale up new deployment by one or more pods (based on maxSurge value)
                - Wait for readiness checks to complete
            - Scale down old deployment by one or more pods (based on maxUnavailable value)
            - Repeat scaling until new deployment reaches desired replica count and old deployment scales to zero
            - Execute any post life-cycle hooks
        - When scaling down, strategy waits for pods to become ready
            - Decides whether further scaling would affect availability
        - If scaled-up pods never become ready, deployment times out
            - Results in deployment failure
    - Recreate Strategy
        - Has basic rollout behavior
        - Supports life-cycle hooks for injecting code into deployment process
        - Steps in Recreate strategy deployment:
            - Execute pre life-cycle hook
            - Scale down previous deployment to zero
            - Scale up new deployment
            - Execute post life-cycle hook
    - Custom Deployment Strategy
        - Example
            ```
            "strategy": {
                "type": "Custom",
                "customParams": {
                    "image": "organization/strategy",
                    "command": ["command", "arg1"],
                    "environment": [
                    {
                        "name": "ENV_1",
                        "value": "VALUE_1"
                    }
                    ]
                }
                }
            ```
    - Builds and S2I
        - Builds
            - Process of transforming input parameters into resulting object
                - Most often used to transform input parameters or source code into runnable image
                - BuildConfig object: Definition of entire build process
                - OpenShift leverages Kubernetes
                    - Creates containers from build images
                    - Pushes containers to integrated registry
        - Build Strategies
            - Build system in OpenShift provides extensible support for build strategies
                - Selectable strategies specified in build API
            - Four primary build strategies:
                - Container build
                - Source-to-Image (S2I) build
                - Custom build
                - Pipeline build (deprecated)
            - Object resulting from build depends on builder used to create it
                - Container and S2I: Runnable images
                - Custom: Whatever builder image author specified
                - Pipeline: Jenkins pipelines executable by Jenkins Pipeline plug-in (deprecated)
            - Container Build
                - Container build strategy invokes plain podman build command
                - Expects repository with Dockerfile and artifacts required to produce runnable image
            - Source-to-Image (S2I) Build
                - S2I: Tool for building reproducible container images
                - Produces ready-to-run images
                    - Injects application source into container image, assembles new container image
                - New image incorporates base image (builder) and built source
                - Ready to use with podman run command
            - S2I supports incremental builds
                - Reuses previously downloaded dependencies, previously built artifacts, etc.
    - S2I Build Advantages
        - Image flexibility
            - S2I scripts can inject application code into most container images, exploiting existing ecosystem. Uses tar to inject application source.
        - Speed
            - Assemble process performs large number of complex operations without creating new layer at each step
            - S2I scripts reuse artifacts stored in previous version of application image
        - Patchability
            - Rebuild application consistently if underlying image needs patch due to security issue
        - Operational efficiency
            - Restrict build operations and prevent arbitrary actions allowed by Dockerfiles
            - Avoid accidental or intentional abuses of build system
    - Custom Build
        - Custom build strategy: You define specific builder image responsible for entire build process
            - Using own builder image allows you to customize build process
        - Custom builder image is plain OCI-compliant container image embedded with build process logic
            - Examples: Building RPMs or base container images
    - Image Stream
        - Comprises OCI-compliant container images identified by tags
        - Presents single virtual view of related images
            - Similar to container image repository
        - May contain images from any of following:
            - Image repository in integrated OpenShift Container Platform registry
            - Other image streams
            - Container image repositories from external registries
        - New images added to associated image stream
        - Builds and deployments watch image stream
            - Receive notifications when new images added
            - React by performing build or deployment
    - OpenShift Service Mesh
        - Enhances deployment options for microservices
        - CI/CD systems provide minimal configuration information
    - Istio
        - Layer for service-to-service communication
        - Manages discovery, load balancing, failure recovery, metrics, monitoring
        - Microservices operations support for A/B testing, canary rollouts, rate limiting, access control, end-to-end authentication
        - Implemented as array of lightweight proxies injected as sidecar containers
    - OpenShift Serverless Technology
        - Trigger Deployments on application events
        - Knative serving
        - Knative eventing
        - Camel K + connectors (Evenet Sources)
        - Kafka + Strimzi
    - OpenShift Serverless Technology
        - Serving
            - From container to URL within seconds
            - Easier developer experience for Kubernetes
            - Built-in versioning, traffic split, and more
            - Simplified installation experience with Kourier
            - Automatic TLS/SSL for applications
    - Pipelines
        - Tekton-based Pipelines in Technology Preview
            - Serverless CI/CD
        - Jenkins jobs enabled by Pipeline plug-in
            - Built with simple text scripts that use Pipeline DSL based on Groovy
    - Pipeline Characteristics
        - Durable
            - Survive planned and unplanned restarts of Jenkins master
        - Pausable
            - Optionally stop and wait for human input or approval before completing jobs
        - Versatile
            - Support complex real-world CD requirements
                - Ability to fork or join
                - Ability to loop
                - Ability to work in parallel with other pipelines
        - Extensible
            - Plug-in supports custom extensions to its DSL, multiple integration options with other plug-ins
