### on master and slave server
## vim /etc/haproxy/haproxy.cfg
listen Stats-Page 
  bind *:8000 
  mode http 
  stats enable 
  stats hide-version 
  stats refresh 10s 
  stats uri / 
  stats show-legends 
  stats show-node 
frontend fe-apiserver 
   bind 0.0.0.0:6443 
   mode tcp 
   option tcplog 
   default_backend be-apiserver 
backend be-apiserver 
   mode tcp 
   option tcp-check 
   balance roundrobin 
   default-server inter 10s downinter 5s rise 2 fall 2 slowstart 60s maxconn 250 maxqueue 256 weight 100 
   server control-plane-1 192.168.73.134:6443 check 
   server control-plane-2 192.168.73.135:6443 check 
   server control-plane-3 192.168.73.136:6443 check 

  ## haproxy -c -f  /etc/haproxy/haproxy.cfg 
  ## Systemctl restart haproxy 

cat <<EOT > /etc/keepalived/keepalived.conf 
global_defs { 
   enable_script_security 
   script_user root 
} 
vrrp_script check_haproxy { 
   script "killall -0 haproxy" 
   interval 2 
   weight 2 
   } 
vrrp_instance KUBE_API_LB { 
   state MASTER 
   interface ens33 #change it
   virtual_router_id 51 
   priority 101 
   #The virtual ip address shared between the two loadbalancers 
   virtual_ipaddress { 
       192.168.56.100/32 
   } 
   track_script { 
      check_haproxy 
   } 
} 
EOT 


## on slave server
cat <<EOT > /etc/keepalived/keepalived.conf 
global_defs { 
   enable_script_security 
   script_user root 
} 
#Script used to check if HAProxy is running 
vrrp_script check_haproxy { 
   script "killall -0 haproxy" 
   interval 2 
   weight 2 
} 
vrrp_instance KUBE_API_LB { 
   state BACKUP 
   interface #### ens160  #change it
   virtual_router_id 51 
   priority 100 
   virtual_ipaddress { 
      ${vip_api}/32 #change it
   } 
   track_script { 
      check_haproxy 
   } 
} 
EOT 
