# MySQL Deployment in Kubernetes

## **0. Deploy BASIC Security**

```sh
cd openshift-adv/Day3/0.Security/basics
oc apply -f ./users 
oc apply -f ./ 
```
This applies the roles restrickted rules to the user, deploys Namespaces and DENYALL NetworkPolicy.

## **1. Deploying MySQL using Kustomize**
Kustomize allows for flexible deployments across different environments. Deploy MySQL in `db` for namespaces `dev`, `test`, and `prod` using:

```sh
cd openshift-adv/Day3/0.Security/kustomize
oc apply -k overlays/dev
oc apply -k overlays/test
oc apply -k overlays/prod
```

This applies the respective configurations for each namespace.

## **2. Testing Database Connection Between Namespaces**
To verify MySQL connectivity between environments:

### **2.1 Open a Shell in the MySQL Pod (dev Namespace)**
```sh
oc exec -it mysql-0 -n db -- sh
```

### **2.2 Get the IP Address of a MySQL Pod in `prod`**
```sh
oc get pods -n db -o wide
```
Find the `IP` of the target MySQL prod pod from the output.

### **2.3 Connect to MySQL in `prod` from `dev`**
```sh
mysql -h <POD-IP> -P 3306 -u root -p
```

🔹 **Password:** `password`

### **2.4 Verify MySQL Connection Status**
Once connected, check the database status using:
```sh
\s
```
This command provides details about the MySQL server, including uptime, connections, and threads.
You will not be able to connect to different database - security by networkpolicy.

### **2.5 Delete/Deactivate the networkpolicies for DB-Namespaces**

oc delete networkpolicy default-deny-all -ndb
oc delete networkpolicy allow-mysql-ingress-prod -ndb

repeat steps from 2.4


This ensures a structured deployment, security and enables easy management across different environments. 

---
**Copyright (c) 2025 by Alexander Kolin. All rights reserved.**