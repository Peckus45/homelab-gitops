# Troubleshooting Homelab Kubernetes

## 1. Vérifier l'état global

```bash
kubectl get nodes
kubectl get pods -A
```

## 2. API Kubernetes inaccessible

Symptômes :

```text
connection refused 192.168.1.103:6443
```

Vérifier containerd :

```bash
sudo crictl --runtime-endpoint unix:///run/containerd/containerd.sock ps -a
sudo journalctl -u kubelet -n 80 --no-pager
```

## 3. Containerd sandbox corruption

Symptômes :

```text
failed to reserve sandbox name
CreatePodSandboxError
```

Fix :

```bash
sudo systemctl stop kubelet
sudo systemctl stop containerd

sudo rm -rf /run/containerd/io.containerd.runtime.v2.task/k8s.io/*
sudo rm -rf /var/lib/containerd/io.containerd.grpc.v1.cri/sandboxes/*
sudo rm -rf /var/lib/containerd/io.containerd.grpc.v1.cri/containers/*

sudo systemctl start containerd
sleep 20
sudo systemctl start kubelet
```

## 4. MetalLB VIP inaccessible

Symptômes :

```text
ip neigh show 192.168.1.240
FAILED
```

Contournement stable :

```text
NPM -> NodeIP:NodePort
192.168.1.103:31796
```

