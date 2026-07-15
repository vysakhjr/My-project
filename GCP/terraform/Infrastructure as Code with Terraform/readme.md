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

<h2> Build Infrastructure</h2>
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


