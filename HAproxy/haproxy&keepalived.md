#vim /etc/haproxy/haproxy.cfg
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
