# CMPE 283 - Assignment I

1. Create GCP account and create project
2. Once a Project is created enable “Compute Engine API” for that project to create and manage Virtual Machines.
3. Once we enable Compute Engine API, Create Virtual Machine Instance using the below command. (also enable nested-virtualization)

   gcloud compute instances create instance-1 --project=cmpe-283-virtualization-367909 --zone=us-central1-a --machine-type=n2-standard-8
   --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --provisioning-model=STANDARD
   --service-account=1053649804908-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/cloud-platform
   --tags=http-server,https-server --min-cpu-platform=Intel\ Cascade\ Lake
   --create-disk=auto-delete=yes,boot=yes,device-name=instance-1,image=projects/ubuntu-os-cloud/global/images/ubuntu-1804-bionic-v20221018,mode=rw,size=100,type=projects/cmpe-283-virtualization-367909/zones/us-central1-a/diskTypes/pd-ssd
   --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any
   --enable-nested-virtualization

4. Once an instance is created, open the instance using “gcloud compute ssh <<instance_name>>”.
   <img width="614" alt="Screen Shot 2022-11-07 at 2 34 12 AM" src="https://user-images.githubusercontent.com/30898620/200289484-f7906a53-50d1-4320-878a-c7bdddddba15.png">
5. Once we get into the instance, clone the git repo after changing the “cmpe283-1.c” file. For all the MSRs you can find the code here.
6. File cmpe-281 includes:
   1. Primary Process based controls (IA32_VMX_PROCBASED_CTLS)
   2. Secondary process based controls (IA32_VMX_PROCBASED_CTLS2)
   3. Secondary process based controls (IA32_VMX_PROCBASED_CTLS2)
   4. Entry controls (IA32_VMX_ENTRY_CTLS)
   5. Cannot include tertiary controls as it has Can set=No in primary controls
7. Install necessary packages (mainly make) in the root user by using the “apt install gcc make” command.
8. Check the kernel version and install that version using the below commands in the screenshot.
   <img width="620" alt="Screen Shot 2022-11-07 at 2 35 50 AM" src="https://user-images.githubusercontent.com/30898620/200289837-d5c06035-3856-4a1d-b2c1-505934d5ffe9.png">
9. Execute the “make” command to create the kernel object.
10. Execute sudo “insmod cmpe 283-1.ko” command to load the kernel object.
11. Now, To view the output use the “dmesg” command.
    <img width="621" alt="Screen Shot 2022-11-07 at 2 36 10 AM" src="https://user-images.githubusercontent.com/30898620/200289972-6860e85e-fe97-4d8d-a7ac-24e91bc32b7d.png">
    <img width="622" alt="Screen Shot 2022-11-07 at 2 36 27 AM" src="https://user-images.githubusercontent.com/30898620/200289992-110fcad0-a435-4396-b0e0-d0274c32b8a5.png">
    <img width="617" alt="Screen Shot 2022-11-07 at 2 36 43 AM" src="https://user-images.githubusercontent.com/30898620/200290004-549f5356-3644-4075-a476-5a81b829b7d5.png">
