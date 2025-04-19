 Here's a standard Ansible project folder structure, along with where Ansible by default looks for files and settings:


---

Typical Ansible Project Structure

my-ansible-project/
├── ansible.cfg                # Ansible configuration file (optional, but commonly used)
├── inventory/                 # Inventory files or directories
│   ├── hosts                  # Inventory file (INI or YAML format)
│   └── group_vars/           # Group-specific variables
│       └── webservers.yml
│   └── host_vars/            # Host-specific variables
│       └── server1.yml
├── playbook.yml              # Main playbook file
├── roles/                    # Roles directory
│   └── myrole/
│       ├── tasks/
│       │   └── main.yml      # Tasks for this role
│       ├── handlers/
│       │   └── main.yml
│       ├── templates/
│       │   └── config.j2
│       ├── files/
│       │   └── script.sh
│       ├── vars/
│       │   └── main.yml      # Role-specific vars (high precedence)
│       ├── defaults/
│       │   └── main.yml      # Role-specific defaults (low precedence)
│       ├── meta/
│       │   └── main.yml
│       └── README.md
├── vars/                     # Extra variable files (used with vars_files)
│   └── common.yml
├── templates/                # Jinja2 templates
│   └── app.conf.j2
├── files/                    # Static files
│   └── myfile.txt
└── group_vars/              # Group-specific variables (alternative to being inside inventory/)
    └── all.yml


---

Where Ansible Looks by Default:


---

Variable Precedence (Low to High)

1. Role defaults


2. Inventory group_vars and host_vars


3. Playbook vars


4. Playbook vars_files


5. Role vars


6. Extra vars (--extra-vars)




---


When the same variable (key or setting) appears in multiple places, Ansible uses a defined precedence order to determine which value wins. In most cases, it does NOT merge, it overwrites with the value from the source with higher precedence.


---

Ansible Variable Precedence: (Low to High)

Below is a simplified precedence list (from lowest to highest). The highest one wins:

1. Role defaults (roles/rolename/defaults/main.yml)


2. Inventory group_vars (like group_vars/all.yml, group_vars/web.yml)


3. Inventory host_vars (like host_vars/server1.yml)


4. Playbook vars_files


5. Playbook vars block


6. Role vars (roles/rolename/vars/main.yml)


7. Block vars (defined in a block inside the playbook)


8. Task vars (defined inline inside tasks)


9. Extra vars (--extra-vars) ← Highest




---

Overwrite vs Merge

Default behavior: Overwrite, not merge.

If a variable is defined in multiple places, Ansible does not combine values — it uses the one with highest precedence.

Example:

# group_vars/all.yml
myvar: "value-from-group"

# playbook.yml vars section
vars:
  myvar: "value-from-playbook"

→ Final value of myvar = "value-from-playbook" (because playbook vars > group_vars)


Merging is only supported in specific cases, like with dictionaries (YAML 1.2 merge keys) and only when explicitly coded to do so, using Ansible 2.8+ features like:

defaults: &defaults
  var1: one
  var2: two

override:
  <<: *defaults
  var2: override

→ var2 becomes "override", while var1 remains "one".

Or use combine() in Jinja:

merged_dict: "{{ dict1 | combine(dict2) }}"



---

Summary


---


