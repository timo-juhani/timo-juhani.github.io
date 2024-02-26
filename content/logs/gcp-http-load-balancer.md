+++
title = 'GCP: HTTP Load Balancer'
date = 2024-02-26T12:02:24+02:00
draft = false
tags = ["cloud", "gcp"]
+++

Earlier today I had an opportunity to setup and test a HTTP load balancer on Google Cloud Platform (GCP). I'm new to GCP and have only very limited hands-on experience on load balancers. Struggle was real. I try to explain it to myself next using the public documentation offered here as a skeleton:

<https://github.com/Qwiklearning/Challenge-lab/blob/main/GSP313%20Create%20and%20Manage%20Cloud%20Resources%3A%20Challenge%20Lab>

## Compute

It starts by creating an instance-template that can be used to replicate VMs that share the same configurations. The template here is a super basic one based on the requirements of GSP313 challenge. 

```
cloud compute instance-templates create web-server-template \
          --metadata-from-file startup-script=startup.sh \
          --machine-type e2-medium
```

Then let's create a MIG or a managed instance group. MIG is for creating multiple indentical VMs that host the application. MIG comes with features that help to scale up or down the service or heal from an incident. In this load balancing excerise using a MIG is natural choice because we just want to replicate the same application on multiple VMs. 

```
gcloud compute instance-groups managed create lb-backend-group \
          --size 2 \
          --template web-server-template 
```

Firewall rule to allow HTTP traffic is required. Opening port 80 for all backend VMs to punch a hole for the incoming web traffic. Frankly, it's quite a huge hole for it allows all sources to chat with out web VMs. Which might be bad.. or not depending on the application.  

```
gcloud compute firewall-rules create web-server-firewall \
          --allow tcp:80 \
```

That gives us the settings for two web VMs. To pool them together we need a load balancer. Otherwise publishing the application consisting of multiple VMs behind a single endpoint becomes quite a engineering task. In this case HTTP load balancer is used instead of network load balancer (NLB) which is another common option. If we opted for using an NLB we could only distribute the traffic based on IP and TCP port which might not give enough flexibility in the long run. If the application was super simple an NLB might be an option as well. 

## Load Balancing

Health check is enabled so that we can determine whether the backend VMs respond to traffic correctly. Without health checks there wouldn't be a mechanism for the load balancer to know which VM instances are doing ok. A broken instance could be a potential traffic black hole. That's why we start by defining that object first.

### Backend

```
gcloud compute http-health-checks create http-basic-check
```

Modify the previously created MIG by assigning named ports. The configuration below assigns the name HTTP for port 80. This is an indication that HTTP service runs in all instances of MIG called lb-backend-group. 

```
gcloud compute instance-groups managed \
          set-named-ports lb-backend-group \
          --named-ports http:80
```

Create a backend service for the web servers. A backend service defines how to distribute the traffic to the two web servers. Adding to the backend service assigns our web servers to the load balancer. In this case the servers were defined by using an instance group. In other words there are two identical web servers behding the load balancer, they offer services using HTTP protocol and are monitored by the load balancer by a health check.

```
# Backend service with a health check.
gcloud compute backend-services create web-server-backend \
          --protocol HTTP \
          --http-health-checks http-basic-check \
          --global

# Adding the web server instance-group to the backend service.
gcloud compute backend-services add-backend web-server-backend \
          --instance-group lb-backend-group \
          --global
```

### Frontend

URL map is created to route the incoming requests to the default backend service. In effect this is routing of web requests to their corresponding backend services. The target proxy configuration enables the termination of incoming client connections at the load balancer frontend while creating new connections from load balancer backend to the web servers. Putting URL map and target proxy settings together allows a way to connect clients' HTTP requests via proxy to the individual web server. 

```
# URL map
gcloud compute url-maps create web-server-map \
          --default-service web-server-backend

# Proxy
gcloud compute target-http-proxies create http-lb-proxy \
          --url-map web-server-map
```

Forwarding rule is created to direct the traffic to a load balancer which was defined above. In this case all traffic arriving to HTTP port will be forwarded to the load balancer which will then route the incoming web requests to the web servers. 

```
gcloud compute forwarding-rules create http-content-rule \
        --global \
        --target-http-proxy http-lb-proxy \
        --ports 80
```


Lessons learned:
- Understanding what you do is the most important.
- And for that reason the lack of my load balancing knowledge really turned this one into a mind bender for a moment.
- Don't go forward until you understand.