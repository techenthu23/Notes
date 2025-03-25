# Ansible


An Ansible Playbook is a YAML file that defines a set of tasks to automate system configurations, deployments, or orchestration. Below is the typical structure of an Ansible playbook:


---

Basic Structure of an Ansible Playbook

---
- name: Example Playbook
  hosts: web_servers
  become: yes  # Run tasks as sudo/root
  vars:
    package_name: nginx

  tasks:
    - name: Install a package
      apt:
        name: "{{ package_name }}"
        state: present
      when: ansible_os_family == "Debian"

    - name: Ensure service is running
      service:
        name: "{{ package_name }}"
        state: started
        enabled: yes


---

Breakdown of Playbook Structure

1. YAML Header (---):

Indicates the start of the YAML document.



2. List of Plays (- name:):

Defines what actions will be performed on which hosts.



3. Hosts (hosts:):

Specifies target machines (inventory group or hostname).



4. Privilege Escalation (become: yes):

Runs commands as root (sudo).



5. Variables (vars:):

Defines reusable variables.



6. Tasks (tasks:):

A sequence of steps to execute on the target machine.



7. Conditional Execution (when:):

Runs a task only when the condition is met.





---

Extended Structure with Handlers, Roles, and Templates

---
- name: Deploy Web Server
  hosts: web_servers
  become: yes

  vars:
    package_name: nginx
    index_file: /var/www/html/index.html

  tasks:
    - name: Install Nginx
      apt:
        name: "{{ package_name }}"
        state: present
      notify: Restart Nginx

    - name: Deploy HTML Template
      template:
        src: index.html.j2
        dest: "{{ index_file }}"

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted


---

Additional Components

Handlers (handlers:):

Define actions (e.g., restart a service) that are executed when notified by tasks.


Templates (template:):

Used to deploy Jinja2 templates dynamically.


Roles (roles:):

Modular way to organize complex playbooks (stored in roles/ directory).
# 




In Ansible, **tasks** and **handlers** are fundamental concepts used to automate and manage configurations and deployments.

### Tasks
- **Tasks** are the basic units of action in Ansible playbooks. They define a single action to be performed on the managed nodes, such as installing a package, copying a file, or starting a service.
- Tasks are executed in the order they are defined in the playbook.
- Each task can include various modules, which are the actual units of work (e.g., `yum`, `apt`, `service`, `copy`).

Here's a simple example of a task that installs Apache:

```yaml
tasks:
  - name: Install Apache
    ansible.builtin.yum:
      name: httpd
      state: present
```

### Handlers
- **Handlers** are special types of tasks that are only run when notified by other tasks. They are typically used to perform actions that should only occur if a change is made, such as restarting a service after a configuration file is updated.
- Handlers are defined similarly to tasks but are placed in a separate `handlers` section.
- They are executed at the end of the playbook run, after all tasks have been completed.

Here's an example of a handler that restarts Apache:

```yaml
tasks:
  - name: Install Apache
    ansible.builtin.yum:
      name: httpd
      state: present
    notify: Restart Apache

handlers:
  - name: Restart Apache
    ansible.builtin.service:
      name: httpd
      state: restarted
```

In this example, the `Restart Apache` handler will only run if the `Install Apache` task makes a change (e.g., if Apache was not previously installed) ¹².

Do you have any specific use cases or further questions about Ansible?

Source: Conversation with Copilot, 2/8/2024
(1) Handlers: running operations on change - Ansible Documentation. https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_handlers.html.
(2) How To Define and Use Handlers in Ansible Playbooks. https://www.cherryservers.com/blog/how-to-define-and-use-handlers-in-ansible-playbooks.
(3) Ansible: Tasks vs Roles vs Handlers - Roelof Jan Elsinga. https://roelofjanelsinga.com/articles/ansible-difference-between-tasks-and-roles/.
(4) How To Use Handlers In Ansible Playbooks - OSTechNix. https://ostechnix.com/use-handlers-in-ansible-playbooks/.
(5) en.wikipedia.org. https://en.wikipedia.org/wiki/Ansible_(software).


In Ansible, you can pass variables to tasks in several ways. Here are some common methods:

### 1. Defining Variables in the Playbook
You can define variables directly within your playbook using the `vars` section:

```yaml
- hosts: all
  vars:
    my_var: "Hello, World!"
  tasks:
    - name: Print variable
      ansible.builtin.debug:
        msg: "{{ my_var }}"
```

### 2. Using Inventory Variables
Variables can be defined in the inventory file:

```ini
[webservers]
server1 ansible_host=192.168.1.1 my_var="Hello from inventory"
```

### 3. Passing Variables via the Command Line
You can pass variables when running the playbook using the `-e` or `--extra-vars` option:

```sh
ansible-playbook my_playbook.yml -e "my_var='Hello from command line'"
```

### 4. Using Variable Files
Variables can be stored in separate YAML files and included in the playbook:

```yaml
- hosts: all
  vars_files:
    - vars.yml
  tasks:
    - name: Print variable
      ansible.builtin.debug:
        msg: "{{ my_var }}"
```

`vars.yml`:

```yaml
my_var: "Hello from vars file"
```

### 5. Registering Variables
You can register the output of a task as a variable and use it in subsequent tasks:

```yaml
- hosts: all
  tasks:
    - name: Get date
      ansible.builtin.command: date
      register: date_output

    - name: Print date
      ansible.builtin.debug:
        msg: "{{ date_output.stdout }}"
```

### 6. Using Role Defaults and Vars
When using roles, you can define default variables in `defaults/main.yml` and role-specific variables in `vars/main.yml`.

### 7. Including Variables in Tasks
You can also pass variables to included tasks:

```yaml
- hosts: all
  tasks:
    - include_tasks: my_tasks.yml
      vars:
        my_var: "Hello from included tasks"
```

`my_tasks.yml`:

```yaml
- name: Print variable
  ansible.builtin.debug:
    msg: "{{ my_var }}"
```

These methods allow you to manage and pass variables flexibly in Ansible¹².

Do you have a specific scenario in mind where you need to pass variables?

Source: Conversation with Copilot, 2/8/2024
(1) Using Variables — Ansible Community Documentation. https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html.
(2) How to pass variables to included tasks in ansible?. https://stackoverflow.com/questions/32232221/how-to-pass-variables-to-included-tasks-in-ansible.
(3) How can I write variables inside the tasks file in ansible. https://stackoverflow.com/questions/22522985/how-can-i-write-variables-inside-the-tasks-file-in-ansible.
(4) Learn how to use Ansible variables with examples – 4sysops. https://4sysops.com/archives/learn-how-to-use-ansible-variables-with-examples/.
(5) undefined. http://x.com/file-.
(6) undefined. http://www.my.url.com.


What are some best practices for writing efficient Ansible code?
How do I use conditionals in Ansible tasks?
Can you explain more about roles and playbooks in Ansible?



In Ansible, conditionals allow you to control the execution of tasks based on specific criteria. The most common way to use conditionals is with the `when` statement. Here are some examples to illustrate how you can use conditionals in Ansible tasks:

### Basic Conditional with `when`
You can use the `when` statement to run a task only if a certain condition is met. For example, you might want to install a package only if the operating system is CentOS:

```yaml
tasks:
  - name: Install Apache on CentOS
    ansible.builtin.yum:
      name: httpd
      state: present
    when: ansible_facts['os_family'] == "RedHat"
```

### Using Variables in Conditionals
You can also use variables in your conditionals. For example, you might want to run a task only if a variable `my_var` is set to `true`:

```yaml
tasks:
  - name: Print a message if my_var is true
    ansible.builtin.debug:
      msg: "my_var is true"
    when: my_var
```

### Conditionals Based on Registered Variables
You can register the output of a task and use it in a conditional for a subsequent task:

```yaml
tasks:
  - name: Check if a file exists
    ansible.builtin.stat:
      path: /path/to/file
    register: file_stat

  - name: Print a message if the file exists
    ansible.builtin.debug:
      msg: "The file exists"
    when: file_stat.stat.exists
```

### Multiple Conditions
You can combine multiple conditions using logical operators like `and` and `or`:

```yaml
tasks:
  - name: Print a message if both conditions are true
    ansible.builtin.debug:
      msg: "Both conditions are true"
    when: ansible_facts['os_family'] == "RedHat" and my_var
```

### Using `when` with Loops
You can also use conditionals within loops:

```yaml
tasks:
  - name: Install packages only if they are not already installed
    ansible.builtin.yum:
      name: "{{ item }}"
      state: present
    loop:
      - httpd
      - nginx
    when: ansible_facts['os_family'] == "RedHat"
```

### Conditional Imports and Includes
You can conditionally include or import tasks based on certain conditions:

```yaml
tasks:
  - name: Include tasks if a condition is met
    include_tasks: other_tasks.yml
    when: my_var
```

These examples demonstrate how you can use conditionals to make your Ansible playbooks more dynamic and responsive to different environments and situations¹².

Do you have a specific scenario in mind where you need to use conditionals?

Source: Conversation with Copilot, 2/8/2024
(1) Conditionals — Ansible Community Documentation. https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html.
(2) Ansible Conditionals - When, Register & Other Examples. https://spacelift.io/blog/ansible-when-conditional.
(3) Ansible Conditional Behavior Based on the Output of a ... - Baeldung. https://www.baeldung.com/linux/ansible-command-output-conditional.
(4) undefined. https://jinja.palletsprojects.com/en/latest/templates/.


What are some best practices for writing efficient Ansible code?
Can you explain more about roles and playbooks in Ansible?
How do I manage secrets securely in Ansible?


I've updated the Ansible playbook to use a template for .condarc that allows for custom configurations and includes custom Conda channels.


---

Enhancements in this version:

✔ Uses Jinja2 template for .condarc
✔ Allows adding custom channels
✔ Includes disk space checks before and after installation


---
 Playbook: Manage Miniconda, Conda Config, and Packages

---
- name: Manage Miniconda, Conda Configuration, and Packages
  hosts: all
  become: yes
  vars:
    miniconda_version: "latest"
    miniconda_installer: "Miniconda3-{{ miniconda_version }}-Linux-x86_64.sh"
    miniconda_url: "https://repo.anaconda.com/miniconda/{{ miniconda_installer }}"
    install_dir: "/opt/miniconda"
    tmp_dir: "/tmp/miniconda_tmp"
    conda_user: "ubuntu"  # Change as needed
    conda_group: "ubuntu"
    conda_env_name: "myenv"
    conda_python_version: "3.9"
    conda_packages:
      - numpy
      - pandas
      - scikit-learn
    conda_channels:
      - defaults
      - conda-forge
    conda_settings:
      auto_activate_base: false
      show_channel_urls: true
      channel_priority: strict
    condarc_path: "/home/{{ conda_user }}/.condarc"
    required_free_space_mb: 500  # Minimum 500MB required
    required_free_space_percentage: 10  # At least 10% free space post-install

  tasks:
    - name: Check available disk space
      command: df --output=avail -m /
      register: disk_space
      changed_when: false

    - name: Fail if there is less than 500MB free
      fail:
        msg: "Insufficient disk space! Required: 500MB, Available: {{ disk_space.stdout_lines[1] }}MB"
      when: disk_space.stdout_lines[1] | int < required_free_space_mb

    - name: Check if Miniconda is installed
      stat:
        path: "{{ install_dir }}/bin/conda"
      register: conda_exists

    - name: Install required dependencies
      apt:
        name: curl
        state: present
      when: not conda_exists.stat.exists

    - name: Download Miniconda installer
      get_url:
        url: "{{ miniconda_url }}"
        dest: "/tmp/{{ miniconda_installer }}"
        mode: '0755'
      when: not conda_exists.stat.exists

    - name: Install Miniconda with TMPDIR set
      shell: |
        /bin/bash /tmp/{{ miniconda_installer }} -b -p {{ install_dir }}
      args:
        creates: "{{ install_dir }}/bin/conda"
      environment:
        TMPDIR: "{{ tmp_dir }}"
      when: not conda_exists.stat.exists

    - name: Check post-installation disk space
      command: df --output=pcent /
      register: post_install_space
      changed_when: false

    - name: Fail if less than 10% free space remains
      fail:
        msg: "Post-installation disk space too low! Less than {{ required_free_space_percentage }}% remaining."
      when: post_install_space.stdout_lines[1] | regex_replace('%', '') | int > (100 - required_free_space_percentage)

    - name: Set permissions and ownership for Miniconda base directory
      file:
        path: "{{ install_dir }}"
        owner: "{{ conda_user }}"
        group: "{{ conda_group }}"
        mode: "0755"
        recurse: yes

    - name: Add Miniconda to system PATH
      lineinfile:
        path: "/etc/profile.d/conda.sh"
        line: 'export PATH="{{ install_dir }}/bin:$PATH"'
        create: yes
        state: present
        mode: '0644'

    - name: Remove Miniconda installer
      file:
        path: "/tmp/{{ miniconda_installer }}"
        state: absent
      when: not conda_exists.stat.exists

    - name: Initialize Conda for shell sessions
      shell: |
        {{ install_dir }}/bin/conda init
      args:
        executable: /bin/bash

    - name: Check if Conda environment exists
      command: "{{ install_dir }}/bin/conda env list --json"
      register: conda_envs
      changed_when: false

    - name: Create Conda environment if not exists
      shell: |
        {{ install_dir }}/bin/conda create --name {{ conda_env_name }} python={{ conda_python_version }} -y
      when: "'{{ conda_env_name }}' not in conda_envs.stdout"

    - name: Install and upgrade Conda packages in the environment
      shell: |
        {{ install_dir }}/bin/conda install --name {{ conda_env_name }} {{ conda_packages | join(' ') }} -y
        {{ install_dir }}/bin/conda update --name {{ conda_env_name }} --all -y

    - name: Configure Conda settings in .condarc
      template:
        src: condarc.j2
        dest: "{{ condarc_path }}"
        owner: "{{ conda_user }}"
        group: "{{ conda_group }}"
        mode: "0644"

    - name: Apply Conda configuration settings
      shell: |
        {% for setting, value in conda_settings.items() %}
        {{ install_dir }}/bin/conda config --set {{ setting }} {{ value }}
        {% endfor %}
      args:
        executable: /bin/bash


---

2️⃣ Jinja2 Template for .condarc (condarc.j2)

Save this template in your Ansible playbook directory:

channels:
{% for channel in conda_channels %}
  - {{ channel }}
{% endfor %}
auto_activate_base: {{ conda_settings.auto_activate_base }}
show_channel_urls: {{ conda_settings.show_channel_urls }}
channel_priority: {{ conda_settings.channel_priority }}


---

3️⃣ What This Playbook Does

✅ Checks for free disk space (min 500MB required)
✅ Ensures at least 10% free space remains after installation
✅ Installs Miniconda only if not already installed
✅ Creates a Conda environment (myenv)
✅ Installs & upgrades Conda packages (numpy, pandas, scikit-learn)
✅ Uses a template (condarc.j2) for managing .condarc settings
✅ Adds custom Conda channels (defaults, conda-forge)


---

4️⃣ Run the Playbook

ansible-playbook -i inventory.ini manage_miniconda.yml

Creating a Custom Ansible Module

Ansible allows you to write custom modules in Python to extend its capabilities. Here's how you can create a custom Ansible module step by step.


---

1️⃣ Directory Structure

custom_modules/
│── library/
│   ├── my_custom_module.py   # Custom module script
│── playbook.yml               # Ansible playbook using the module

library/ → This is where Ansible looks for custom modules.

my_custom_module.py → Python script for your module.

playbook.yml → Playbook that calls the module.



---

2️⃣ Writing the Custom Module (Python)

Create library/my_custom_module.py with the following content:

#!/usr/bin/python

from ansible.module_utils.basic import AnsibleModule

def main():
    module_args = dict(
        name=dict(type='str', required=True),
        age=dict(type='int', required=False, default=25)
    )

    module = AnsibleModule(argument_spec=module_args)

    name = module.params['name']
    age = module.params['age']

    message = f"Hello {name}, you are {age} years old."

    module.exit_json(changed=False, message=message)

if __name__ == '__main__':
    main()


---

3️⃣ Calling the Custom Module in a Playbook

Create playbook.yml:

---
- name: Test Custom Module
  hosts: localhost
  tasks:
    - name: Call my_custom_module
      my_custom_module:
        name: "Alice"
        age: 30
      register: result

    - debug:
        msg: "{{ result.message }}"


---

4️⃣ Running the Playbook

ansible-playbook -i localhost, playbook.yml


---

5️⃣ Expected Output

TASK [debug]
ok: [localhost] => {
    "msg": "Hello Alice, you are 30 years old."
}


---

6️⃣ Explanation

✅ Uses AnsibleModule for input validation
✅ Accepts parameters (name, age)
✅ Returns structured JSON output using module.exit_json()


---

7️⃣ Adding Custom Module to Ansible

To make the module globally available:

export ANSIBLE_LIBRARY=~/custom_modules/library

Would you like an example of a real-world module, such as managing Miniconda? 🚀

In Ansible, you can handle default values and conditional execution based on whether a variable is provided. Here’s how:


---

1️⃣ Setting Default Values Using default()

If a variable is not provided, assign a default value:

- name: Use default values if not provided
  debug:
    msg: "User {{ name | default('Guest') }} is {{ age | default(30) }} years old."

✅ If name is not set, it defaults to "Guest".
✅ If age is not set, it defaults to 30.


---

2️⃣ Skipping a Task If a Variable Is Missing (when)

If a variable is not provided, skip execution using when:

- name: Run only if 'username' is provided
  debug:
    msg: "Hello {{ username }}"
  when: username is defined

✅ If username is not provided, the task is skipped.


---

3️⃣ Skipping a Task If the Variable Is Empty (when: var | length > 0)

Sometimes, a variable may be defined but empty ("", null, or []). To skip the task in such cases:

- name: Run only if 'package_name' is not empty
  debug:
    msg: "Installing package {{ package_name }}"
  when: package_name | length > 0

✅ If package_name is an empty string (""), the task won’t run.


---

4️⃣ Using fail If a Variable Is Mandatory

If execution must stop when a required variable is missing, use fail:

- name: Stop playbook if 'env' variable is missing
  fail:
    msg: "ERROR: 'env' variable is required but not provided."
  when: env is not defined or env | length == 0

✅ If env is missing or empty, the playbook stops immediately.


---

5️⃣ Combining Defaults & Conditional Execution

You can mix default() and when to make execution dynamic:

- name: Example of both default and conditional execution
  debug:
    msg: "Deploying in environment {{ env | default('development') }}"
  when: env is defined and env | length > 0

✅ If env is not provided, it defaults to "development".
✅ If env is empty, the task is skipped.


---

🔹 Real-World Example: Miniconda Installation

- name: Install Miniconda only if enough disk space is available
  shell: wget -O /tmp/miniconda.sh "{{ miniconda_url | default('https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh') }}"
  when: disk_space_mb | default(0) | int > 500

✅ If disk_space_mb is not provided, defaults to 0 (task will be skipped).
✅ If disk_space_mb > 500, Miniconda is downloaded.


---

Would you like me to refine this for your specific use case (e.g., Miniconda playbook)? 🚀

