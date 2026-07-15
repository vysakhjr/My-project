<h1> Infrastructure as Code with Terraform </h1>
<p> Login to cloudshell. Authorize Cloud Shell to use your credentials to make Google Cloud API calls </p>
<img width="1657" height="899" alt="image" src="https://github.com/user-attachments/assets/991394f8-f634-4d5a-8325-58084ed9b05d" />

Checking the active account name
<img width="838" height="279" alt="image" src="https://github.com/user-attachments/assets/7f401a9c-a638-474e-9d7b-ca2a610d97ca" />

You can list the project id with this command
<img width="1692" height="160" alt="image" src="https://github.com/user-attachments/assets/8c416f90-f13a-4520-a33a-c0e7f1e9a6e0" />

<h2> Install Terraform </h2>
Terraform is not pre-installed in Cloud Shell. You must install the Terraform CLI and configure it to persist across your Cloud Shell sessions.

1. To configure the HashiCorp repository and install Terraform, and to ensure that this installation persists across future sessions, run the following commands in the Cloud Shell terminal:
cat <<'EOF' > ~/.customize_environment
# Set up HashiCorp repository and install Terraform
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install -y terraform
EOF
bash ~/.customize_environment

2. To verify that Terraform has been installed correctly, run:
terraform --version
<img width="701" height="56" alt="image" src="https://github.com/user-attachments/assets/f8eb9f43-f1e4-4757-b5b6-89c627b9c97c" />

<h1> Build Infrastructure</h1>
1. create a main.tf file. Terraform recognizes files ending with main.tf and main.tf.json as configuration files and loads them when it runs.
2. Click the Open Editor button on the toolbar of Cloud Shell. (You can switch between Cloud Shell and the code editor by using the Open Editor and Open Terminal icons as required, or click the Open in new window button to leave the Editor open in a separate tab).
3. In the Editor, add the following content to the main.tf file.
<img width="465" height="408" alt="image" src="https://github.com/user-attachments/assets/344c02b2-ab87-4e20-b4d6-0225c2f2c54c" />

<h2> initialization</h2>
Run terraform init to initialize the new terraform configuration file
<img width="699" height="331" alt="image" src="https://github.com/user-attachments/assets/b0b6fc13-df80-4e83-9390-c81e1d620d85" />

<h2> Create resource</h2>
1. Apply you configuration now by running the command terraform apply:
The output has a + next to resource "google_compute_network" "vpc_network", meaning that Terraform will create this resource. Beneath that, it shows the attributes that will be set. When the value displayed is (known after apply), it means that the value won't be known until the resource is created.

If the plan was created successfully, Terraform will now pause and wait for approval before proceeding. If anything in the plan seems incorrect or dangerous, it is safe to abort here with no changes made to your infrastructure.

If terraform apply failed with an error, read the error message and fix the error that occurred.

2. The plan looks acceptable here, so type yes at the confirmation prompt, and press ENTER to proceed.
Executing the plan takes a few minutes since Terraform waits for the network to be created successfully:
<img width="1102" height="515" alt="image" src="https://github.com/user-attachments/assets/15501472-b310-4132-824b-20d79f698688" />

3. After this, Terraform is all done! You can go to the Cloud console to see the network you have provisioned.
<img width="1117" height="251" alt="image" src="https://github.com/user-attachments/assets/ff8458b6-3c96-4eaf-8049-e70e22a51f6f" />

4. Run terraform show to know the current state
<img width="1198" height="197" alt="image" src="https://github.com/user-attachments/assets/8a3d3275-a587-45ef-ad64-860f9511e85d" />

<h1> Change the infrastructure </h1>
<h2> Adding resource</h2>
You can add new resources by adding them to your Terraform configuration and running terraform apply to provision them.
1. In the Editor, add a compute instance resource to main.tf:
resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "e2-micro"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-12"
    }
  }

  network_interface {
    network = google_compute_network.vpc_network.name
    access_config {
    }
  }
}

This resource includes a few more arguments. The name and machine type are simple strings, but boot_disk and network_interface are more complex blocks. You can see all of the available options in the google_compute_instance documentation.

For this example, your compute instance will use a Debian operating system, and will be connected to the VPC Network you created earlier. Notice how this configuration refers to the network's name property with google_compute_network.vpc_network.name -- google_compute_network.vpc_network is the ID, matching the values in the block that defines the network, and name is a property of that resource.

The presence of the access_config block, even without any arguments, ensures that the instance is accessible over the internet.

2. Run terraform apply
<img width="1104" height="755" alt="image" src="https://github.com/user-attachments/assets/76a30bd3-e066-4f47-9edc-d5f736244cc7" />

<h2> Changing resources</h2>
In addition to creating resources, Terraform can also make changes to those resources.
1. Add a tags argument to your vm_instance resource in the main.tf file so that it looks like this:
resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "e2-micro"
  tags         = ["web", "dev"] # Add this line
  # ...
}

2. Run terraform apply
<img width="1161" height="427" alt="image" src="https://github.com/user-attachments/assets/6f52447f-0901-4857-bfbc-280934792bc6" />

<h2>Destructive changes</h2>
A destructive change is a change that requires the provider to replace the existing resource rather than updating it. This usually happens because the cloud provider doesn't support updating the resource in the way described by your configuration.

Changing the disk image of your instance is one example of a destructive change.
1. Edit the boot_disk block inside the vm_instance resource in your main.tf configuration file and change it to the following:
<img width="315" height="96" alt="image" src="https://github.com/user-attachments/assets/78f24974-fa77-453c-a943-5d926ab691c0" />

2. Run terraform apply

The prefix -/+ means that Terraform will destroy and recreate the resource, rather than updating it in-place. While some attributes can be updated in-place (which are shown with the ~ prefix), changing the boot disk image for an instance requires recreating it. Terraform and the Google Cloud provider handle these details for you, and the execution plan makes it clear what Terraform will do.

Additionally, the execution plan shows that the disk image change is what required your instance to be replaced. Using this information, you can adjust your changes to possibly avoid destroy/create updates if they are not acceptable in some situations.

<h2>Destroy infrastructure</h2>
1. Try the terraform destroy command. Answer yes when prompted to execute this plan and destroy the infrastructure:
![Uploading image.png…]()

The - prefix indicates that the instance and the network will be destroyed. As with apply, Terraform shows its execution plan and waits for approval before making any changes.

Just like with terraform apply, Terraform determines the order in which things must be destroyed. Google Cloud won't allow a VPC network to be deleted if there are resources still in it, so Terraform waits until the instance is destroyed before destroying the network. When performing operations, Terraform creates a dependency graph to determine the correct order of operations. In more complicated cases with multiple resources, Terraform performs operations in parallel when it's safe to do so.

<h1>Create resource dependencies</h1>

<h2>Assign a static IP address</h2>
Add the following to your main.tf configuration file to assign a static IP to the VM instance:
resource "google_compute_address" "vm_static_ip" {
  name = "terraform-static-ip"
}
