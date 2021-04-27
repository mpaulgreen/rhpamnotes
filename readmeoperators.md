- Controllers follow a simple control loop.
    - Observe - They get the current state of a resource such as POD. They using open shift api to get the number of PODS.
    - Analyze - They get the desired state of the POD using open shift api that are supposed to runnning.
    - Act - They take corrective action if there is a difference between desired and actual behavior. In case there is a difference, they call API to correct the numbers
- OpenShift Operator
    - Custom Controller that manages OpenShift resources.
    - Typically, an Operator manages an entire application. Therefore, it manages a variety of resource, not just PODS.
    - For example, an Operator can control a pod, a secret, a ConfigMap, a service, a route, and a PersistentVolumeClaim that together make up the full application.
    - An Operator can use stock controllers, such as ReplicaSets, to control the pods. However, in most cases, the Operator manages the pods itself.
- Domain-/Application-Specific Knowledge
    - Unlike a stock OpenShift controller, an Operator can have domain- or application-specific knowledge embedded in the control logic.
    - For example, the controller may take special steps to scale an application properly, in the same way that a StatefulSet controller scales an application differently from a Deployment.
    - Operators can also handle automatic upgrades of the underlying application, as well as backup, restore, and many other mundane management tasks
- Sometimes additional tasks are needed beyond install, upgrade, scale, back up, and self-heal.
- The more powerful the Operator, the more of these tasks it can handle instead of requiring human administrators to manage them.
- In OpenShift, it is possible to define custom resources, or CRs, via custom resource definitions, or CRDs.
- Interraction with OpenShift
    - CRDs allow the extension of the OpenShift API.
    - This means that the API now understands resources that are custom-made, not just those built into OpenShift.
    - An Operator watches for the creation of CR objects that conform to the API specification defined in the CRD.
    - After a CRD is created, the OpenShift API reacts to commands manipulating these custom resources
        - For example, if your CRD adds a definition for a Tomcat object, it is possible to run commands such as "oc get tomcats" or "oc delete tomcat mytomcat".
- Operator = CRD + Custom Controller + Domain Specific Knowledge
- Reference - https://github.com/operator-framework
- Operator Framework 
    - The Operator Framework is designed to provide a streamlined, uniform way of writing and managing Operators, which can be deployed to any Kubernetes cluster, not just OpenShift
    - Operator SDK - Build Operators - no specialized knowledge API required.
    - Operator Lifecycle Manager - Install, Update and manage operator and their dependencies.  
    - Operator Metering - Enable usage reporting for Operators.
- Operator Components
    - Container Image
        - The container image contains the code for the Operator.
        - This image must be accessible to the OpenShift cluster in a public registry like Quay.io or Docker.io. It can also be in the OpenShift integrated image registry.
    - OpenShift/Kubernetes API Objects
        - A CRD needs to be created.
        - A service account needs to be created.
        - An RBAC Role object that grants required permissions for the Operator.
        - An RBAC RoleBinding object that binds the role to the service account.
        - A deployment object to run the Operator container image. This deployment uses the service account to run under and provides the service account with the correct permissions to the container.
- Operator Installation
    - Can install Operators for either:
        - Entire Cluster
        - Single Project
    - Depending on scope, different RBAC is required.
    - Depending on scope, the operator watches either one project or entire cluster for CR creation events.
    - It is possible to create a ClusterRole that automatically grants permission to create CR objects to every user ID that has project administrator permissions.
    - For some steps of the installation process, "cluster-admin" permissions are required.
- Installation Steps for Cluster Wide Operator
    - Create the CRD that the Operator manages. Because CRDs are cluster resources, "cluster-admin" authorization is necessary to deploy and create a new CRD in a cluster.
    - Create a project for the Operator to run in.
    - Create a service account for the Operator pod to run with.
    - Create an RBAC ClusterRole object. The cluster role must contain rules to allow the Operator to manipulate every OpenShift resource that it wants to control.
    - Create an RBAC ClusterRoleBinding object to grant the permissions in the cluster role to the service account running the Operator pod.
    - Create an Operator deployment object by pointing to the container image that contains the Operator code.
    - Set the "WATCH_NAMESPACE" environment variable on the Deployment to an empty string. This indicates that this Operator watches all projects in the cluster.
- Installation Steps for Namespaced Operator
    - Create the CRD that the Operator manages. Remember that "cluster-admin" authorization is necessary to deploy and create a new CRD in a cluster.
        - The remaining steps can be executed either as a project "admin" or, optionally, with "cluster-admin" permissions.
    - Create a project for the Operator to run in.
    - Create a service account for the Operator pod to run with.
    - Create an RBAC role object. The role must contain rules to allow the Operator to manipulate every OpenShift resource that it wants to control in the project.
    - Create an RBAC RoleBinding object to grant the permissions in the role to the service account running the Operator pod.
    - Create an Operator deployment object by pointing to the container image that contains the Operator code.
    - Set the "WATCH_NAMESPACE" environment variable on the deployment to the current project name.
        - To set automatically, use downwardAPI metadata.namespace field
- Example of CRD
```
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: tomcats.apache.org
spec:
  group: apache.org
  names:
    kind: Tomcat
    listKind: TomcatList
    plural: tomcats
    singular: tomcat
    shortNames:
    - tc
  scope: Namespaced
  version: v1alpha1 

```
- Example of RBAC Role - If not using cluster-admin permissions, OpenShift requires explicit list of resources and verbs
```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: tomcat-operator
rules:
- apiGroups:
  - tomcats.apache.org
  resources:
  - "*"
  verbs:
  - "*"
- apiGroups:
  - ""
  resources:
  - "*"
  verbs:
  - "*"
```
- Example of RBAC Role Binding
```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: default-account-tomcat-operator
subjects:
- kind: ServiceAccount
  name: tomcat-operator
roleRef:
  kind: Role
  name: tomcat-operator
  apiGroup: rbac.authorization.k8s.io
```

- To add permission to every project admin to create role to access CRD from project:

```
apiVersion: authorization.openshift.io/v1
kind: ClusterRole
metadata:
  labels:
    rbac.authorization.k8s.io/aggregate-to-admin: "true"
  name: gogs-admin-rules
rules:
- apiGroups:
  - gpte.opentlc.com
  resources:
  - gogs
  verbs:
  - create
  - update
  - delete
  - get
  - list
  - watch
  - patch
```
- Operator Deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-operator
spec:
  replicas: 1
  selector:
    matchLabels:
      name: tomcat-operator
  template:
    metadata:
      labels:
        name: tomcat-operator
    spec:
      serviceAccountName: tomcat-operator
      containers:
        - name: tomcat-operator
          image: quay.io/wkulhanek/tomcat-operator:v0.1.0
          imagePullPolicy: Always
          env:
            - name: WATCH_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
```

- Operator Deletion
     - Delete Operator deployment
     - Delete RBAC RoleBinding or RBAC ClusterRoleBinding
     - Delete RBAC role or RBAC ClusterRole
     - Delete service account
     - Delete CRD
     - Delete project if empty
- Custom Resource Creation using an Operator
    - An Operator that is running in an OpenShift cluster is not really useful by itself. It only starts acting when CRs are created in a project that it watches.
    - Creating a CR triggers an event. For the CR creation to be successful, the CRD needs to be available in the cluster.
    - If there is no Operator running that watches for specific events associated with this CR, creating the CR does absolutely nothing.
    - When an Operator receives the event that a CR has been created, the Operator executes its control logic to set up all of the API objects that make up the application that the Operator manages.