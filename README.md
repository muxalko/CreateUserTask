
## CreateUserTask
Here is my little Jenkins pipeline test, which involves using Ansible Playbooks and Python script.


Main stages:
* Collect and validate user input
* Create user and add public.key to authorized_keys
* Generate system report


### Prerequisites

* Ansible 2.9 
* Python3+
* Ansible plugin

* In order to securely provide sudo password for our privileged user we need to create passwd.yml file that will include our details 

* create passwd.yml in workspace
  ```sh
  ansible-vault create passwd.yml
  ```

* Inside passwd.yml put 
  ```sh
  root_username: user
  root_password: password
  ```

Now, when the file is encrypted, the password for the vault should be uploaded as a secret file into Jenkins credentials with ID "VaultPasswdFile"

Dashboard->Credentials->System->Global credentials (unrestricted)->VaultPasswdFile

### Installation

1. Create a new Pipeline in Jenkins.
2. Add this repository as a source
3. Create passwd.yml file in workspace.
4. Upload vault password with secret file into new credential with ID "VaultPasswdFile"
3. Press Build
4. Open the build log and wait for the inputRequest

## Usage

When pipeline is requesting input: 

Provide the following information
1. username 
2. public.key 
3. choose a method for running ansible playbook.

In the end of the pipeline, there should be a new user with public.key in authorized_keys added.
Workspace should include readme_bob.txt file with read-only permissions for newly created user.
There should also be job artifact containing data.json file generated.

The difference between ansible methods: 
* shell module - will utilize shell commands to be able to sudo as different user, both jenkins user and user should be sudoers, user should have NOPASSWD: inside /etc/sudoers.
* ansible modules - will use root , jenkins user must be a sudoer


