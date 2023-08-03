# Pi-hole Kubernetes Deployment
This repository contains Kubernetes manifests to deploy Pi-hole with Unbound, an open-source network-wide ad blocker that acts as a DNS sinkhole. It provides network-level blocking of ads, trackers, and malware for all devices connected to the network.

## Prerequisites
- Kubernetes cluster up and running.

## Installation

1. Apply the Kubernetes manifests using `kubectl`:

```sh
kubectl apply -f pihole.yaml
```

## Configuration

The following components will be deployed in the pihole namespace:

### Namespace

Creates a Kubernetes namespace for the Pi-hole deployment.

### ConfigMap
A ConfigMap to store Pi-hole configuration settings.
- `TZ`: The timezone setting (e.g., Europe/Amsterdam).
- `DNS1`: The DNS server to use (e.g., 127.0.0.1#5335).
- `VIRTUAL_HOST`: The virtual host for Pi-hole (e.g., pi-hole.example.com).

### Secret
A Secret to store sensitive Pi-hole settings.
- `WEBPASSWORD`: The base64-encoded password for the Pi-hole web interface.

### Deployment
A Kubernetes Deployment that runs Pi-hole and Unbound containers.

- Pi-hole Container:
  - Image: pihole/pihole:latest
  - Environment Variables from ConfigMap and Secret.
  - Ports: 53 (TCP/UDP), 67 (UDP), 80 (TCP).
  - Mounts configuration volumes.

- Unbound Container:
  - Image: klutchell/unbound:latest
  - Runs as a privileged container.
  - Ports: 5335 (TCP/UDP).
  - Mounts Unbound configuration.

### Service
A NodePort Service to expose Pi-hole to external IP addresses.

- Type: NodePort
- External IPs: The cluster IP addres: (e.g., 192.168.0.1).
- Ports:
  - 53 (TCP/UDP)
  - 67 (UDP)
  - 80 (TCP)

## Accessing Pi-hole
To access the Pi-hole web interface, use the NodePort and external IPs specified in the Service. For example, if one of the external IPs is 192.168.0.1 and the port is 80, you can access Pi-hole at http://192.168.0.1:80/admin.

## Cleanup
To remove the Pi-hole deployment, run the following command:
```sh
kubectl delete -f pihole.yaml
```

## Contributing
We welcome contributions and bug reports! Please create an issue or submit a pull request if you encounter any problems or want to improve the deployment.