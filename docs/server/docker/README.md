# Docker

## Install Docker on Winsow 11
running wslexec: An error occurred while running the command. DockerDesktop/Wsl/ExecError: c:\windows\system32\wsl.exe --version: exit status 1 (stderr: The Windows Subsystem for Linux is not installed. You can install by running 'wsl.exe --install'.

For more information please visit https://aka.ms/wslinstall

, stdout: , wslErrorCode: DockerDesktop/Wsl/ExecError)


```
docker run --name postgres_container `
  -e POSTGRES_PASSWORD=your_secure_password `
  -d `
  -p 5432:5432 `
  -v postgres_data:/var/lib/postgresql/data `
  postgres
  ```

```bash
docker run --name my-postgres -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 -v pgdata:/var/lib/postgresql/data -d postgres
```

```
 docker container logs c514be41ec58
Error: in 18+, these Docker images are configured to store database data in a
       format which is compatible with "pg_ctlcluster" (specifically, using
       major-version-specific directory names).  This better reflects how
       PostgreSQL itself works, and how upgrades are to be performed.

       See also https://github.com/docker-library/postgres/pull/1259

       Counter to that, there appears to be PostgreSQL data in:
         /var/lib/postgresql/data (unused mount/volume)

       This is usually the result of upgrading the Docker image without
       upgrading the underlying database using "pg_upgrade" (which requires both
       versions).

       The suggested container configuration for 18+ is to place a single mount
       at /var/lib/postgresql which will then place PostgreSQL data in a
       subdirectory, allowing usage of "pg_upgrade --link" without mount point
       boundary issues.

       See https://github.com/docker-library/postgres/issues/37 for a (long)
       discussion around this process, and suggestions for how to do so.
```       

```
docker run --name postgres17 -e POSTGRES_PASSWORD=your_secure_password -p 5432:5432 -v pgdata:/var/lib/postgresql/data -d postgres:17
```