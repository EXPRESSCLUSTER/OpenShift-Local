# Setup OpenShit Local with KVM behind Proxy Sever

## Prerequisite
- Refer to [Hardware requirements](https://access.redhat.com/documentation/en-us/red_hat_openshift_local/2.5/html/getting_started_guide/installation_gsg).
  - The above page says that you need 9 GiB RAM but the default value for memory in crc.json is 16 GiB.
    ```json
    {
      "consent-telemetry": "yes",
      "cpus": 6,
      "memory": 16384,
      "no-proxy": "127.0.0.1,192.168.130.11"
    }
    ```

## Evaluation Environment
### CRC 2.9.0
```
+----------------------------------------------------+
| Host OS                                            |
| - CentOS Linux release 7.9.2009 (Core)             |
| - KVM (virsh version)                              |
|   - Compiled against library: libvirt 4.5.0        |
|   - Using library: libvirt 4.5.0                   |
|   - Using API: QEMU 4.5.0                          |
|   - Running hypervisor: QEMU 2.12.0                |
| +------------------------------------------------+ |
| | Guest OS                                       | |
| | - CRC (crc version)                            | |
| |   - CRC version: 2.9.0+9591a8f                 | |
| |   - OpenShift version: 4.11.3                  | |
| |   - Podman version: 4.2.0                      | |
| | - OpenShift (oc version)                       | | 
| |   - Client Version: 4.11.3                     | |
| |   - Kustomize Version: v4.5.4                  | |
| |   - Server Version: 4.11.3                     | |
| |   - Kubernetes Version: v1.24.0+b62823b        | |
| +------------------------------------------------+ |
+----------------------------------------------------+
```

## Setup OpenShift Local
1. Create a user and add it to the following groups.
   ```sh
   usermod -aG libvirt <user name>
   ```
   ```sh
   usermod -aG wheel <user name>
   ```
1. Switch to the sudoer user.
   ```sh
   su <user name>
   ```
1. Run the following command to run virsh command with sudoer user.
   ```sh
   export LIBVIRT_DEFAULT_URI="qemu:///system"
   ```
1. Log in Red Hat and download CRC binary file (e.g. crc-linux-amd64.tar.xz).
1. Expand the archive file.
   ```sh
   tar xvf crc-linux-amd64.tar.xz
   ```
1. Copy the binary file (crc) to your PATH (e.g. /usr/local/bin).
   ```sh
   sudo cp ./crc-linux-2.9.0-amd64/crc /usr/local/bin
   ```
1. If you have a proxy server, run the following commands.
   ```sh
   crc config set http-proxy <your proxy server>
   ```
   ```sh
   crc config set https-proxy <your proxy server>
   ```
   ```sh
   crc config set no-proxy 127.0.0.1,192.168.130.11
   ```
   ```sh
   crc config set proxy-ca-file <CA file>
   ```
1. Run the following command.
   ```sh
   crc setup
   ```
   You can get the result as below.
   ```
   INFO Using bundle path /home/fukunaga/.crc/cache/crc_libvirt_4.11.3_amd64.crcbundle 
   INFO Checking if running as non-root              
   INFO Checking if running inside WSL2              
   INFO Checking if crc-admin-helper executable is cached 
   INFO Checking for obsolete admin-helper executable 
   INFO Checking if running on a supported CPU architecture 
   INFO Checking minimum RAM requirements            
   INFO Checking if crc executable symlink exists    
   INFO Creating symlink for crc executable          
   INFO Checking if Virtualization is enabled        
   INFO Checking if KVM is enabled                   
   INFO Checking if libvirt is installed             
   INFO Checking if user is part of libvirt group    
   INFO Checking if active user/process is currently part of the libvirt group 
   INFO Checking if libvirt daemon is running        
   INFO Checking if a supported libvirt version is installed 
   INFO Checking if crc-driver-libvirt is installed  
   INFO Checking if systemd-networkd is running      
   INFO Checking if NetworkManager is installed      
   INFO Checking if NetworkManager service is running 
   INFO Checking if /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf exists 
   INFO Writing Network Manager config for crc       
   INFO Using root access: Writing NetworkManager configuration to /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf 
   INFO Using root access: Changing permissions for /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf to 644  
   INFO Using root access: Executing systemctl daemon-reload command 
   INFO Using root access: Executing systemctl reload NetworkManager 
   INFO Checking if /etc/NetworkManager/dnsmasq.d/crc.conf exists 
   INFO Writing dnsmasq config for crc               
   INFO Using root access: Writing NetworkManager configuration to /etc/NetworkManager/dnsmasq.d/crc.conf 
   INFO Using root access: Changing permissions for /etc/NetworkManager/dnsmasq.d/crc.conf to 644  
   INFO Using root access: Executing systemctl daemon-reload command 
   INFO Using root access: Executing systemctl reload NetworkManager 
   INFO Checking if libvirt 'crc' network is available 
   INFO Setting up libvirt 'crc' network             
   INFO Checking if libvirt 'crc' network is active  
   INFO Starting libvirt 'crc' network               
   INFO Checking if CRC bundle is extracted in '$HOME/.crc' 
   INFO Checking if /home/fukunaga/.crc/cache/crc_libvirt_4.11.3_amd64.crcbundle exists 
   Your system is correctly setup for using CRC. Use 'crc start' to start the instance
   ```
1. Start OpenShift Local.
   ```sh
   crc start
   ```
   You can get the result as below.
   ```
   INFO Checking if running as non-root              
   INFO Checking if running inside WSL2              
   INFO Checking if crc-admin-helper executable is cached 
   INFO Checking for obsolete admin-helper executable 
   INFO Checking if running on a supported CPU architecture 
   INFO Checking minimum RAM requirements            
   INFO Checking if crc executable symlink exists    
   INFO Checking if Virtualization is enabled        
   INFO Checking if KVM is enabled                   
   INFO Checking if libvirt is installed             
   INFO Checking if user is part of libvirt group    
   INFO Checking if active user/process is currently part of the libvirt group 
   INFO Checking if libvirt daemon is running        
   INFO Checking if a supported libvirt version is installed 
   INFO Checking if crc-driver-libvirt is installed  
   INFO Checking if systemd-networkd is running      
   INFO Checking if NetworkManager is installed      
   INFO Checking if NetworkManager service is running 
   INFO Checking if /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf exists 
   INFO Checking if /etc/NetworkManager/dnsmasq.d/crc.conf exists 
   INFO Checking if libvirt 'crc' network is available 
   INFO Checking if libvirt 'crc' network is active  
   INFO Loading bundle: crc_libvirt_4.11.3_amd64...  
   CRC requires a pull secret to download content from Red Hat.
   You can copy it from the Pull Secret section of https://console.redhat.com/openshift/create/local.
   ? Please enter the pull secret (Copy and paste your secret)
   WARN Cannot add pull secret to keyring: The name org.freedesktop.secrets was not provided by any .service files 
   INFO Creating CRC VM for openshift 4.11.3...      
   INFO Generating new SSH key pair...               
   INFO Generating new password for the kubeadmin user 
   INFO Starting CRC VM for openshift 4.11.3...      
   INFO CRC instance is running with IP 192.168.130.11 
   INFO CRC VM is running                            
   INFO Updating authorized keys...                  
   INFO Check internal and public DNS query...       
   INFO Check DNS query from host...                 
   INFO Verifying validity of the kubelet certificates... 
   INFO Starting kubelet service                     
   INFO Waiting for kube-apiserver availability... [takes around 2min] 
   INFO Adding user's pull secret to the cluster...  
   INFO Updating SSH key to machine config resource... 
   INFO Waiting for user's pull secret part of instance disk... 
   INFO Adding proxy configuration to the cluster... 
   INFO Changing the password for the kubeadmin user 
   INFO Updating cluster ID...                       
   INFO Updating root CA cert to admin-kubeconfig-client-ca configmap... 
   INFO Starting openshift instance... [waiting for the cluster to stabilize] 
   INFO 3 operators are progressing: image-registry, operator-lifecycle-manager-packageserver, service-ca 
   INFO 2 operators are progressing: image-registry, kube-controller-manager 
   INFO 2 operators are progressing: image-registry, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 3 operators are progressing: image-registry, kube-apiserver, kube-controller-manager 
   INFO 2 operators are progressing: image-registry, kube-apiserver 
   ERRO Cluster is not ready: cluster operators are still not stable after 15m12.725233591s 
   INFO Waiting for the proxy configuration to be applied... 
   INFO Adding crc-admin and crc-developer contexts to kubeconfig... 
   ERRO Cannot update kubeconfig: Head "https://oauth-openshift.apps-crc.testing": EOF 
   Started the OpenShift cluster.
   
   The server is accessible via web console at:
     https://console-openshift-console.apps-crc.testing
   
   Log in as administrator:
     Username: kubeadmin
     Password: uRi54-wp9zg-hruf8-VpEms
   
   Log in as user:
     Username: developer
     Password: developer
   
   Use the 'oc' command line interface:
     $ eval $(crc oc-env)
     $ oc login -u developer https://api.crc.testing:6443   
   ```
