# Demo cluster deployment

1. Install *kubectl*

   ```console
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/ kubectl"
   ```

   ```console
   curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
   ```

   ```console
   echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
   ```

   ```console
   sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
   ```

2. Install *minikube*

   *Latest*:

   ```console
   curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x  minikube && sudo mv minikube /usr/local/bin/
   ```

   *v1.24.0*:

   ```console
   curl -Lo minikube https://storage.googleapis.com/minikube/releases/v1.24.0/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
   ```

3. Install *conntrack* library
   
   ```console
   sudo apt-get install -y conntrack
   ```

4. Deployed Minikube, considering 2GB default memory isn't always enough

   ```console
   minikube start --driver=none --memory=4096
   ```