# Ansible Role: PostgreSQL Setup

## Description
This Ansible role automates the installation and configuration of PostgreSQL in a master-replica environment. It handles:

- Installation of required packages and Python dependencies.
- Configuration of PostgreSQL repositories.
- Initialization of PostgreSQL clusters and setting up data directories.
- Configuring master and replica nodes for replication.
- Creation of PostgreSQL users and databases.

## Requirements
- Supported OS: Ubuntu/Debian-based systems.
- Python 3 and `pip3` installed on target systems.
- PostgreSQL version specified in the `pg_version` variable.
- Proper network setup for replication (e.g., connectivity between master and replica nodes).

## Role Variables

| Variable                          | Description                                                                 | Default/Example                       |
|-----------------------------------|-----------------------------------------------------------------------------|---------------------------------------|
| `pg_version`                      | PostgreSQL version to install.                                             | `14`                                  |
| `pg_data_dir`                     | Custom data directory for PostgreSQL.                                      | `/var/lib/postgresql/{{ pg_version }}`|
| `pg_default_data_dir`             | Default PostgreSQL data directory.                                         | `/var/lib/postgresql/{{ pg_version }}`|
| `pg_listen_addresses`             | IP addresses PostgreSQL listens on.                                        | `'localhost'`                         |
| `pg_port`                         | Port PostgreSQL listens on.                                                | `5432`                                |
| `pg_replication.enabled`          | Enable replication setup.                                                  | `false`                               |
| `pg_replication.replication_user` | Replication user name.                                                     | `replicator`                          |
| `pg_replication.replication_password` | Replication user password.                                                | `password`                            |
| `pg_users`                        | List of PostgreSQL users to create.                                        | See example below.                    |
| `pg_databases`                    | List of PostgreSQL databases to create.                                    | See example below.                    |
| `postgres_role`                   | Role of the current host (`master` or `replica`).                          | `master`                              |
| `pg_master_host`                  | Hostname or IP of the master node (for replicas).                          | `master.example.com`                  |
| `postgresql_repo_path`            | Path to PostgreSQL repository script.                                      | `/usr/share/postgresql-common/pgdg`   |
| `postgresql_key_url`              | URL for downloading PostgreSQL signing key.                                | `https://www.postgresql.org/key`      |
| `postgresql_sources_list`         | Path to PostgreSQL sources list file.                                      | `/etc/apt/sources.list.d/pgdg.list`   |
| `postgresql_repo_url`             | PostgreSQL repository base URL.                                            | `http://apt.postgresql.org/pub/repos` |

### Example `pg_users` and `pg_databases`
```yaml
pg_users:
  - name: app_user
    password: app_password

pg_databases:
  - name: app_db
    owner: app_user
```

## Dependencies
No external dependencies are required.

## Usage
Include this role in your playbook and set the required variables.

### Example Playbook
```yaml
- hosts: all
  roles:
    - role: postgresql_setup
      vars:
        pg_version: 14
        pg_data_dir: /custom/data/dir
        pg_replication:
          enabled: true
          replication_user: replicator
          replication_password: replication_pass
        pg_users:
          - name: app_user
            password: app_password
        pg_databases:
          - name: app_db
            owner: app_user
        postgres_role: master
```

### Running the Role
```bash
ansible-playbook -i inventory postgresql_setup.yml
```

## Role Tasks
### General Workflow
1. Install necessary system packages.
2. Install Python dependencies.
3. Configure PostgreSQL repository and install PostgreSQL.
4. Initialize PostgreSQL data directory and cluster.
5. Configure PostgreSQL settings:
   - Listening addresses.
   - Port number.
   - Data directory.
6. Create users and databases.
7. Configure master node:
   - Enable replication.
   - Set WAL level and maximum WAL senders.
8. Configure replica node:
   - Stop PostgreSQL service.
   - Perform base backup from master.
   - Set up `primary_conninfo` for replication.



