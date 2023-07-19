echo "update and upgrade" 

apt update ; apt upgrade -y 

  

apt install -y wget git vim bash-completion curl htop net-tools dnsutils \ 

atop sudo software-properties-common telnet axel jq iotop 

 

echo -e "Docker Installation"  

which docker || { curl -fsSL https://get.docker.com | bash; } 

{ 

systemctl enable docker 

systemctl restart docker 

systemctl is-active --quiet docker && echo -e "\e[1m \e[96m docker service: \e[30;48;5;82m \e[5mRunning \e[0m" || echo -e "\e[1m \e[96m docker service: \e[30;48;5;196m \e[5mNot Running \e[0m" 

} 

  

 

 

echo "Configur docker daemon " 

DOCKER_DEST=/etc/systemd/system/docker.service.d/ 

if [ -d $DOCKER_DEST ] ; then 

   echo "file exist" 

else 

   mkdir -p /etc/systemd/system/docker.service.d/ 

   touch /etc/systemd/system/docker.service.d/override.conf 

fi    

  

cat <<EOT > /etc/systemd/system/docker.service.d/override.conf 

[Service] 

ExecStart= 

ExecStart=/usr/bin/dockerd --log-opt max-size=100m --log-opt max-file=5 

EOT 

cat /etc/systemd/system/docker.service.d/override.conf 

{ 

systemctl daemon-reload 

systemctl restart docker 

systemctl is-active --quiet docker && echo -e "\e[1m \e[96m docker service: \e[30;48;5;82m \e[5mRunning \e[0m" || echo -e "\e[1m \e[96m docker service: \e[30;48;5;196m \e[5mNot Running \e[0m" 

} 

  

echo "how to fix WARNING: No swap limit support" 

cat /etc/default/grub 

echo "GRUB_CMDLINE_LINUX=\"cgroup_enable=memory swapaccount=1\"" >> /etc/default/grub 

cat /etc/default/grub 

sudo update-grub 

  

containerd config default > /etc/containerd/config.toml 

sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml 

cat /etc/containerd/config.toml | grep SystemdCgroup 

  

# Restart containerd 

sudo systemctl restart containerd 

cat > /etc/crictl.yaml <<- EOF 

 

runtime-endpoint: unix:///run/containerd/containerd.sock 

image-endpoint: unix:///run/containerd/containerd.sock 

timeout: 2 

debug: true 

pull-image-on-create: false 

EOF 

echo "Add sysctl settings" 

cat >>/etc/sysctl.d/kubernetes.conf<<EOF 

net.bridge.bridge-nf-call-ip6tables = 1 

net.bridge.bridge-nf-call-iptables = 1 

EOF 

sysctl --system >/dev/null 2>&1 

  

echo "Disable and turn off SWAP" 

sed -i '/swap/d' /etc/fstab 

swapoff -a 

  

echo "Installing apt-transport-https pkg" 

  

sudo apt-get update 

sudo apt-get install -y apt-transport-https ca-certificates 

 

 curl 

  

sudo curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg 

  

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list 

  

sudo apt-get install -y kubelet kubeadm kubectl 

sudo apt-mark hold kubelet kubeadm kubectl 

  

echo "Enable and start kubelet service" 

systemctl enable kubelet  

systemctl start kubelet   

systemctl status kubelet 

kubeadm config images pull 

sudo kubeadm init --control-plane-endpoint=<VIP-IP> --upload-certs --apiserver-advertise-address=<node-IP> 
 

echo "Deploy Calico network" 

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml 

Join worker 

 

 

mkdir -p $HOME/.kube 

    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config 

    sudo chown $(id -u):$(id -g) $HOME/.kube/config 

    export KUBECONFIG=/etc/kubernetes/admin.conf 

 
