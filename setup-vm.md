## To create a GPU Deep Learning VM 

creation of VM (note: standard instance type, i.e. non-SPOT)
```
gcloud compute instances create instai-05 \
    --project=banded-totality-255615 \
    --zone=us-west4-a \
    --machine-type=n1-standard-2 \
    --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
    --maintenance-policy=TERMINATE \
    --provisioning-model=STANDARD \
    --service-account=348736749705-compute@developer.gserviceaccount.com \
    --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append \
    --accelerator=count=1,type=nvidia-tesla-t4 \
    --tags=http-server,https-server \
    --create-disk=auto-delete=yes,boot=yes,device-name=instai-03,image=projects/ml-images/global/images/c0-deeplearning-common-cu122-v20240514-debian-11,mode=rw,size=150,type=projects/banded-totality-255615/zones/us-west4-c/diskTypes/pd-balanced \
    --no-shielded-secure-boot \
    --shielded-vtpm \
    --shielded-integrity-monitoring \
    --labels=goog-ec-src=vm_add-gcloud \
    --reservation-affinity=any
```

Then setup ssh tunneling
```
gcloud compute ssh instai-05 --zone=us-west4-a -- -L 8080:localhost:8080
```

On first login via ssh (with above CLI), it will prompt to install NVidia driver. proceed to do so. Got the following warnings
```
+ sudo ./driver_installer.run --dkms -a -s --no-drm --install-libglvnd ''
Verifying archive integrity... OK
Uncompressing NVIDIA Accelerated Graphics Driver for Linux-x86_64 510.47.03..........................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................

WARNING: The nvidia-drm module will not be installed. As a result, DRM-KMS will not function with this installation of the NVIDIA driver.


WARNING: nvidia-installer was forced to guess the X library path '/usr/lib64' and X module path '/usr/lib64/xorg/modules'; these paths were not
         queryable from the system.  If X fails to find the NVIDIA X driver module, please install the `pkg-config` utility and the X.Org
         SDK/development package for your distribution and reinstall the driver.
```

creation of VM (note: spot instance type)
```
gcloud compute instances create instai-01 \
    --project=banded-totality-255615 \
    --zone=us-central1-a \
    --machine-type=n1-standard-2 \
    --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
    --no-restart-on-failure \
    --maintenance-policy=TERMINATE \
    --provisioning-model=SPOT \
    --instance-termination-action=STOP \
    --service-account=348736749705-compute@developer.gserviceaccount.com \
    --scopes=https://www.googleapis.com/auth/cloud-platform \
    --accelerator=count=1,type=nvidia-tesla-t4 \
    --tags=http-server,https-server \
    --create-disk=auto-delete=yes,boot=yes,device-name=instai-01,image=projects/ml-images/global/images/c0-deeplearning-common-cpu-v20240514-debian-11,mode=rw,size=150,type=projects/banded-totality-255615/zones/us-central1-f/diskTypes/pd-balanced \
    --no-shielded-secure-boot \
    --shielded-vtpm \
    --shielded-integrity-monitoring \
    --labels=goog-ec-src=vm_add-gcloud \
    --reservation-affinity=any
```

## To create a plain deep learning VM to verify ssh tunneling to jupyter notebook

creation of VM
```
gcloud compute instances create instai-adv \
    --project=root-vortex-424108-d9 \
    --zone=us-central1-a \
    --machine-type=e2-medium \
    --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
    --no-restart-on-failure \
    --maintenance-policy=TERMINATE \
    --provisioning-model=SPOT \
    --instance-termination-action=STOP \
    --service-account=543796254005-compute@developer.gserviceaccount.com \
    --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append \
    --tags=http-server \
    --create-disk=auto-delete=yes,boot=yes,device-name=instai-adv,image=projects/ml-images/global/images/c0-deeplearning-common-cpu-v20230925-debian-10,mode=rw,size=50,type=projects/root-vortex-424108-d9/zones/us-central1-a/diskTypes/pd-balanced \
    --no-shielded-secure-boot \
    --shielded-vtpm \
    --shielded-integrity-monitoring \
    --labels=goog-ec-src=vm_add-gcloud \
    --reservation-affinity=any
```

Then setup ssh tunneling
```
gcloud compute ssh instai-adv --zone=us-central1-a -- -L 8080:localhost:8080
```