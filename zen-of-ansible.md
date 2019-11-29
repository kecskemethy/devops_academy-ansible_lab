The Zen of Ansible

- Ansible is like the Swiss Army Knife of DevOps. Most of the cases both provision and configuration can be done from it.​

- Keep [re-usability](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse.html) in mind, separate roles even in code base so it's easier to share for even different projects, teams etc. But keep it simple: Single role single goal. If you have common variables or tasks for roles, separate and import them.​

- Think declaratively. Try to build roles that they can build from zero but even update an existing env using the same code. (= Idempotence.) Add state to tasks whether possible. Use provided modules, avoid command, shell, raw and script tasks as they might break this rule.​

- Check if [Ansible Galaxy](https://galaxy.ansible.com/) might already have what you try to achieve. Contribute back your improvement ideas.

- Naming convention is nice to have for variables, roles, role directory structure and playbooks. Always name tasks in roles. Add comments when your code needs explanation. These comments, names do self-documentation even so can be source of generated documents. README.md per role is mandatory as well as a main one for the full project!​

- Ansible tags are handy for filtering within role tasks so it's good to have them. Group tasks when they have the same tags or when clause.​

- Use dynamic inventory whenever possible e.g. in cloud environment​

- Variable [precedence](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) is always important to keep in mind so always use variables in the right level but try to keep them in role level. Avoid hard-coded values but use variables and templates.​

- Secrets should be vaulted. Use at least ansible-vault but central vault is even better.​

- Clean up debug messages before commit to version control but log and monitor performance of your playbooks e.g.  using `callback_whitelist = timer, profile_tasks` configuration could give you valuable information.​

- Try to build roles which can run in multiple OS families: `ansible_os_family == ` but remember there are modules which helps and handles this for us internally e.g. [package](https://docs.ansible.com/ansible/latest/modules/package_module.html)​

- Gather facts could slow down playbook runtime. Try to turn it off. `gather_facts: false` but could be necessary when using internal variables.​

References:​
- https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html