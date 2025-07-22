# nics226tuneup

Tune up for Intel Nics I 226-V using proxmox and Mikrotik CHR/x86

This is for my CWWK N100 4 ports Lan 2.5G Intel I 226-V and 2 ports SFP+ Intel 83599ES 


1 . TUNE

      In proxmox shell : nano /etc/network/interfaces
      
      Then add those values under each Nics // Pay attention to the nic name convention, enp1s0f0 or enp1s0...
      
      Ctrl + O to save , and Ctrl + x to exit


For Intel 82599ES:

      auto enp1s0f0
      iface enp1s0f0 inet manual
              post-up /sbin/ethtool -K enp1s0f0 tso off gso off gro off tx off rx off
              post-up /sbin/ethtool -G enp1s0f0 rx 8192 tx 8192
              post-up /sbin/ethtool -C enp1s0f0 rx-usecs 0

    auto enp1s0f1
    iface enp1s0f1 inet manual
            post-up /sbin/ethtool -K enp1s0f1 tso off gso off gro off tx off rx off
            post-up /sbin/ethtool -G enp1s0f1 rx 8192 tx 8192
            post-up /sbin/ethtool -C enp1s0f1 rx-usecs 0


For : Intel I 226-V Nics:

    auto enp5s0
          iface enp5s0 inet manual
                  post-up /sbin/ethtool -K enp5s0 tso off gso off gro off tx off rx off
                  post-up /sbin/ethtool -G enp5s0 rx 4096 tx 4096 
                  post-up /sbin/ethtool --set-eee enp5s0 eee off
            
      auto enp2s0
      iface enp2s0 inet manual
              post-up /sbin/ethtool -K enp2s0 tso off gso off gro off tx off rx off
              post-up /sbin/ethtool -G enp2s0 rx 4096 tx 4096
              post-up /sbin/ethtool --set-eee enp2s0 eee off
      
      auto enp3s0
      iface enp3s0 inet manual
              post-up /sbin/ethtool -K enp3s0 tso off gso off gro off tx off rx off
              post-up /sbin/ethtool -G enp3s0 rx 4096 tx 4096
              post-up /sbin/ethtool --set-eee enp3s0 eee off
      
      auto enp4s0
    iface enp4s0 inet manual
            post-up /sbin/ethtool -K enp4s0 tso off gso off gro off tx off rx off
            post-up /sbin/ethtool -G enp4s0 rx 4096 tx 4096
            post-up /sbin/ethtool --set-eee enp4s0 eee off

Also in this proxmox file, under bridge: // make sure your bridge and bind port name are correct

          auto vmbr0
          iface vmbr1 inet manual
          bridge-ports enp1s0f0
          bridge-stp off
          bridge-fd 0
          bridge-vlan-aware yes
          bridge-vids 2-4094
          post-up /sbin/ethtool -K vmbr0 tso off gso off gro off tx off rx off
        

2. Increase Buffer Sizes - Add to /etc/sysctl.conf :

        net.core.rmem_max=4194304
        net.core.wmem_max=4194304
        net.core.rmem_default=262144
        net.core.wmem_default=262144
        net.ipv4.tcp_rmem=4096 87380 4194304
        net.ipv4.tcp_wmem=4096 65536 4194304
        net.core.netdev_max_backlog=5000
        net.ipv4.tcp_congestion_control=bbr

3. Add multique to VM Hardware Config:
   
         In the ROS VM Hardware Network-Device setting:
         Remove the firewall=1, add multique= # of cpu core.
         Model=VirtIO // some say try E1000 ( for stable ping)

5. Add multive for Hard drive VM config:
   
       In ROS VM Hardware SCSI setting:
       Select : VirtIO SCSI // do not select the VirtIO SCSI-single.


   

