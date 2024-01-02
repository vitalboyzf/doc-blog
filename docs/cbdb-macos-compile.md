---
title: On macOS
---

# Compile and Install Cloudberry Database on macOS

:::info
The source of this document is from the GitHub repository [`cloudberrydb/cloudberrydb`](https://github.com/cloudberrydb/cloudberrydb/blob/main/readmes/README.macOS.md).
:::

This document shares how to build, compile, and install Cloudberry Database on macOS (single node) for development and trial purposes. Follow the steps below.

According to our test, these steps work well on macOS Ventura 13.4+ with both Intel and Apple silicon processors (M1 or M2). If you have an older version of macOS, upgrading is recommended. Make sure that the Mac you use has at least 4 cores and 8 GB memory, and is connected to the Internet.

:::caution
DO NOT use this guide for production deployment.
:::

## Step 1. Install needed dependencies

1. Clone the source code of Cloudberry Database from GitHub to your local Mac.

    ```bash
    git clone git@github.com:cloudberrydb/cloudberrydb.git
    ```

2. Enter the `cloudberrydb/` directory.

    ```bash
    cd cloudberrydb/
    ```

3. Run the following command to install the needed dependencies. You will be asked to enter the `sudo` password of your macOS system.

    ```bash
    source readmes/README.macOS.bash
    ```

    :::info
    This will install [Homebrew](https://brew.sh/) if missing.
    :::

## Step 2. Enable password-free SSH connection to localhost

1. Enable **Remote Login** on your macOS system by navigating to **System Preferences** \> **General** \> **Sharing** \> **Remote Login**.
2. Run the following command to verify whether password-free SSH connection to localhost has been enabled on your operating system.

    ```bash
    ssh $(hostname)
    ```

    - If this command runs without error or requiring you to enter a password, run `exit` and go to [Step 3. Configure, compile, and install](#step-3-configure-compile-and-install).
    - If you are required to enter a password, take the following steps to set up password-free SSH connection.

        1. Run `ssh-keygen` and then `cat ~/.ssh/id_rsa.pub >>  ~/.ssh/authorized_keys`.
        2. Run `ssh $(hostname)` again to check whether password-free connection is ready.
        3. If ready, run `exit` and go to [Step 3. Configure, compile, and install](#step-3-configure-compile-and-install).

:::info

- If it is the first time you are using `ssh` connection to localhost, you might need to accept the trust on the first use prompt:

    ```bash
    The authenticity of host '<your hostname>' can't be established.
    ECDSA key fingerprint is SHA256:<fingerprint here>.
    Are you sure you want to continue connecting (yes/no)?
    ```

- If your hostname does not resolve, try adding your machine name to `/etc/hosts`:

    ```bash
    echo -e "127.0.0.1\t$HOSTNAME" | sudo tee -a /etc/hosts
    ```

:::

## Step 3. Configure, compile, and install

```bash
# Run the following commands under the `cloudberrydb/` dir.
# 1. Configure the build environment.

BREWPREFIX=$(brew --prefix); export PATH="$BREWPREFIX/opt/gnu-sed/libexec/gnubin:$BREWPREFIX/opt/apr/bin:$PATH"; CXXFLAGS="-I $BREWPREFIX/include" CFLAGS="-ggdb -Og -g3 -fno-omit-frame-pointer -I $BREWPREFIX/include" LDFLAGS="-L $BREWPREFIX/lib" CC=$(which gcc-13) CXX=$(which g++-13) ./configure --enable-debug --prefix=$(cd ~; pwd)/install/cbdb;

# 2. Compile and install Cloudberry Database.

make -j8
make -j8 install

# 3. Bring in Greenplum environment for Cloudberry Database into your running shell.

source $(cd ~; pwd)/install/cbdb/greenplum_path.sh

# 4. Install the Python dependencies.

pip3 install --user -r readmes/python-dependencies.txt

# 5. Start a demo cluster.

PORT_BASE=8000 make create-demo-cluster
```

Prepare the test by running the following command. This command will configure the port and environment variables for the test. Environment variables such as `PGPORT` and `COORDINATOR_DATA_DIRECTORY` will be configured, which are the default port and the data directory of the coordinator node.

```bash
source gpAux/gpdemo/gpdemo-env.sh
```

## Step 4. Verify the cluster

1. You can verify whether the cluster has started successfully by running the following command. If successful, you will see many active `postgres` processes with ports ranging from `8000` to `8007`.

    ```bash
    ps -ef | grep postgres
    ```
    
2. Connect to the Cloudberry Database and see the active segment information by querying the system table `gp_segement_configuration`. For detailed description of this table, see the Greenplum document [here](https://docs.vmware.com/en/VMware-Greenplum/7/greenplum-database/ref_guide-system_catalogs-gp_segment_configuration.html).

    ```sql
    $ psql -p 8000 postgres
    postgres=# select version();
    postgres=# select * from gp_segment_configuration;
    ```
    
    Example output:

    ```shell
    $ psql -p 8000 postgres
    psql (14.4, server 14.4)
    Type "help" for help.

    postgres=# select version();
                                                                                             version                                                                                         
    -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
     PostgreSQL 14.4 (Cloudberry Database 1.0.0+1c0d6e2224 build dev) on x86_64-apple-darwin22.4.0, compiled by gcc-13 (Homebrew GCC 13.2.0) 13.2.0, 64-bit compiled on Sep 22 2023 10:56:01
    (1 row)

    postgres=# select * from gp_segment_configuration;
     dbid | content | role | preferred_role | mode | status | port |          hostname           |           address           |                                                 datadir                                                  | warehouseid 
    ------+---------+------+----------------+------+--------+------+-----------------------------+-----------------------------+----------------------------------------------------------------------------------------------------------+-------------
        1 |      -1 | p    | p              | n    | u      | 8000 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/qddir/demoDataDir-1         |           0
        8 |      -1 | m    | m              | s    | u      | 8001 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/standby                     |           0
        3 |       1 | p    | p              | s    | u      | 8003 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/dbfast2/demoDataDir1        |           0
        6 |       1 | m    | m              | s    | u      | 8006 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/dbfast_mirror2/demoDataDir1 |           0
        2 |       0 | p    | p              | s    | u      | 8002 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/dbfast1/demoDataDir0        |           0
        5 |       0 | m    | m              | s    | u      | 8005 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/dbfast_mirror1/demoDataDir0 |           0
        4 |       2 | p    | p              | s    | u      | 8004 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/dbfast3/demoDataDir2        |           0
        7 |       2 | m    | m              | s    | u      | 8007 | cbdb.local | cbdb.local | /Users/cbdb/Documents/GitHub/upstream/cloudberrydb/gpAux/gpdemo/datadirs/dbfast_mirror3/demoDataDir2 |           0
    (8 rows)

    postgres=# 
    ```

3. Now you can run `installcheck-world` to start the cluster:

    ```bash
    # In the folder where your cloned the source code
    make installcheck-world
    ```

Congratulations 🎉! You've successfully installed and created a CloudberryDB cluster. Happy Hacking! 😉
