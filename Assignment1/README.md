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

# CMPE 283 Assignment 2

- The assignment is performed on Google Cloud VM supporting nested virtualiztion(`--enable-nested-virtualization`).
- Configuration:
  - Machine Type: **n2-standard-8**
  - CPU platform: **Intel Cascade Lake**

<br />

- Assignment 2 changes the behavior of the **cpuid** instruction for the following cases:
  - CPUID leaf node(`%eax=0x4fffffff`)
    - Return the total number of exits for all types in **%eax**.
  - CPUID lead node(`%eax=0x4ffffffe`)
    - Return the high 32 bits of the total time spent processing all exits in **%ebx**.
    - Return the low 32 bits of the total time spent processing all exits in **%ecx**.
    - **%ebx** and **%ecx** return values are measured in processor cycles, across all vCPUs.

<br />

## Question 1:

- Varun Reddy Yedulla and Satyadivya Maddipudi

- varun

Created the kernel
In the VM, installed the necessary kernel modules.
Comprehended and researched the assignment's code.
The code has been updated to reflect the changes that were discussed.
Test files were created and compiled.

- Divya

Created the kernel
Reviewed the canvas lecture and understood the steps to be taken
Did some research on CPUID instructions and CPU leaf nodes.
Recognized where and what changes to make in order to complete the assignment
README.md has been updated and documentation has been created.

<br />

## Question 2: (Steps used to complete the assignment)

1. Download the source code in the VM [official kernel website](https://www.kernel.org/).

   ```
   sudo apt-get install wget
   wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.0.7.tar.xz
   ```

2. Extract the code

   ```
   tar xvf linux-6.0.7.tar.xz
   ```

3. Run below command to install all the required packages

   ```
   sudo apt-get install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison
   ```

4. Configuring the kernel by copying the existing configuration file to a `.config` file

   ```
   cd linux-6.0.7
   cp -v /boot/config-$(uname -r) .config
   ```

5. Build the kernel through the following commands:

   - `sudo make modules`
   - `sudo make`
   - While compiling the kernel, an error may occur saying `No rule to make target 'debian/canonical-certs.pem'`, then execute the below commands:
     - `scripts/config --disable SYSTEM_TRUSTED_KEYS`
     - `scripts/config --disable SYSTEM_REVOCATION_KEYS`
   - `sudo make modules_install`
   - `sudo make install`

6. Bootloader is automatically updated because of the `make install` and now we can reboot the system and check the kernel version

   - `sudo reboot`
   - `uname -mrs`
   - Output: **Linux 6.0.7 x86_64**

     ### Please refer images folder

7. Chnage the two files in the linux repo by using the files updated in the repo

   - **vmx.c**: `/linux-6.0.7/arch/x86/kvm/vmx/vmx.c`
   - **cpuid.c**: `/linux-6.0.7/arch/x86/kvm/cpuid.c`

8. Build and installed modules again

   - `sudo make modules && sudo make modules_install`

9. For loading the newly built modules, perform following commands:

   - `sudo rmmod kvm_intel`
   - `sudo rmmod kvm`
   - `sudo modprobe kvm_intel`

10. For testing the functionality, an nested VM was created in the GCP VM. The steps to create an nested VM are as follows:

    - Download the Ubuntu cloud image`.img`(QEMU compatible image) file from this [ubuntu cloud images site] in GCP VM(https://cloud-images.ubuntu.com/).

    ```
    wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
    ```

    - Install the required qemu packages

    ```
    sudo apt update && sudo apt install qemu-kvm -y
    ```

    -Change the password and login into the VM, perform the below commands:

    ```
    1)  sudo apt-get install cloud-image-utils

    2)  cat >user-data <<EOF
        #cloud-config
        password: newpass <<new password here>>
        chpasswd: { expire: False }
        ssh_pwauth: True
        EOF

    3)  cloud-localds user-data.img user-data
    ```

    - Now, to run this ubuntu image, execute this:

    ```
    sudo qemu-system-x86_64 -enable-kvm -hda bionic-server-cloudimg-amd64.img -drive "file=user-data.img,format=raw" -m 512 -curses -nographic
    ```

    - **username**: `ubuntu`
    - **password**: `newpass`

    - Now to check the functionality, we need the cpuid utility package, for that

    ```
    sudo apt-get update
    sudo apt-get install cpuid
    ```

- NOTE: Make sure two terminals are open:
  - the GCP VM terminal(T1)
  - the nested VM terminal(logged in)(T2)

<br />

11. Testing the CPUID functionality for `%eax=0x4fffffff`

- T2: `sudo cpuid -l 0x4fffffff`
  ### Please refer images folder
- T1: `sudo dmesg`
  ### Please refer images folder

12. Testing the CPUID functionality for `%eax=0x4ffffffe`

- T2: `sudo cpuid -l 0x4ffffffe`
  ### Please refer images folder
- T1: `sudo dmesg`
  ### Please refer images folder
