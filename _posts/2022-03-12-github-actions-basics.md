---
---

GitHub actions is a CI/CD platform just like Jenkins, CircleCI and others. Its
very easy to use since it directly integrates with the repository. Just create a
file inside your repo `.github/workflows/workflow_name.yml` and put the
appropriate config. Here's an example workflow:

```yaml
name: ci-test # workflow name

# triggers
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:

  test: # job name
    runs-on: ubuntu-latest # a GitHub hosted runner

    services:
      postgres:
        # Docker Hub image
        image: postgres:11
        # Provide the password for postgres
        env:
          POSTGRES_USER: postgres_user
          POSTGRES_PASSWORD: postgres_password
          POSTGRES_DB: db_name
        ports:
          # Maps tcp port 5432 on service container to the host
          - 5432:5432
        # Set health checks to wait until postgres has started
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
    - uses: actions/checkout@v2 # a reusable action, created by GitHub

    - name: Set up Go
      uses: actions/setup-go@v2 # another reusable action
      with:
        go-version: 1.17

    - name: Install golang-migrate
      run: |
        curl -L https://github.com/golang-migrate/migrate/releases/download/v4.15.1/migrate.linux-amd64.tar.gz | tar xvz
        sudo mv migrate /usr/bin/
        which migrate

    - name: Run migrations
      run: make migrateup

    - name: Test
      run: make test # a custom action where we're running custom commands
```

- **Workflow**: A workflow represents a session.
	- A workflow can be triggered in three ways:
		- Events - push, pr, etc.
		- Scheduled - at specific times (cron jobs)
		- Manually - click on Run workflow button
	- A workflow is composed of one or more **Jobs**.
- **Jobs**
	- Each job runs in parallel to other jobs
	- If `needs: job_name` is specified for any job, then it'll run the `job_name` job first and then run the current job (sequentially).
	- A job is run inside a **Runner**.
	- A job contains one or more **Steps**.
	- A job can be executed inside a container by specifying `container: node:10.18-jessie`.
	- A job may depend on **External Services**. Example: [PostgreSQL service](https://docs.github.com/en/actions/using-containerized-services/creating-postgresql-service-containers)
- **Runner**
	- Runners can be GitHub hosted. Example: `runs-on: ubuntu-latest`
	- Self-hosted
	- Runners report progress, logs and other information to GitHub and then we can check those directly from the repository.
- **Steps**
	- Each step is composed of one or more **Actions**.
- **Actions**
	- Actions define what to perform.
	- Actions can be reused. Example: `uses: actions/checkout@v2`
