# NFS Dynamic provisioner


To use nfs volumes in kubernetes, we have to create a nfs server in any one of the kubernetes cluster node or a dedicated network/storage node. Follow below procedure for nfs installation and making a directory in the nfs server to be accessible across network nodes/or *

We can cross check the accessibility of created nfs storage from the worker/slave nodes using showmount -e nfs-server command. We can even mount to see the directory from nodes as well.

Now, everytime we have to create pv and pvc, and use pvc in any pod configuration... it will use the nfs server stoarge for volume, and it is dynamic... you can change content in nfs server locaiton and check the reflection in nginx websites.. it's working.

Now, using nfs-provisioner deployment, which runs in the background as a pod on any of the node.. we don't have to create a pv evrytime. Using just pvc we can provision the pv dynamically... this helps in larger org when requesting volumes many times...

Note: Always check the Reclaim policy present in created pv.. by default it is DELETE, so if we delete pod or pvc.. entire volume will go off.

## install nfs server

From nfs server:
sudo yum install nfs-utils

sudo mkdir /srv/nfs/kubedata -p
sudo chown nobody: /srv/nfs/kubedata/
sudo systemctl enable nfs-server
sudo systemctl start nfs-server

In /etc/exports file, add entry.. making the directory accessible for * or any network intrstd
/srv/nfs/kubedata       *(rw,sync,no_subtree_check,no_root_squash,insecure)

sudo exportfs -rav
sudo exportfs -v


From kubernetes worker nodes:
check if you can able to access the nfs created from nfs server
showmount -e 172.42.42.100

mount -t nfs nfs-server-ip:/srv/nfs/kubedata /mnt
check mount:
mount | grep kubedata

umount mnt to unmount the export from nfs server

----------------
