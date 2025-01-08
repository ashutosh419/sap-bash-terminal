# sap-bash-terminal

This repo will be a guide to various linux commands which can be helpful to use the bash terminal in VSCode, BAS or any Linux env.

## Using Terminal

1. Binding shortcut to clear the terminal. Below command binds Ctrl + l to the clear command

    ````bash
    bind -x '"\C-l": clear'   
    ````

2. Setting command aliases for shorter commands

    The below code sets only the alias temporary. It needs to be set in ~/bash.rc file.

    ````bash
    alias r='ranger'
    ````

    We can use the ls command with -a OPTION to see the hidden files in Home (~) directory. (FunFact: Home and ~ is used to refer the same because it was the same key in Mainframe keyboard).

    ```bash
    ls -al ~/
    ```

    It would show all the files: 

    ```bash
    total 208
    drwxrwsr-x 1 codespace codespace  4096 Jan  3 09:18 .
    drwxr-xr-x 1 root      root       4096 Dec 31 15:38 ..
    -rw-r--r-- 1 codespace codespace    17 Jan  3 09:18 .bash_aliases
    -rw------- 1 codespace codespace 22186 Jan  7 05:37 .bash_history
    -rw-rw-r-- 1 codespace codespace   220 Feb 25  2020 .bash_logout
    -rw-rw-r-- 1 codespace codespace  6086 Jan  2 13:11 .bashrc
    ... and so on
    ```

    Edit the file or create the file .bash_aliases which is referred in the .bashrc file. (modularisation)
    Use Vim or Nano or Code to do this.

    ```bash
    cd ~/
    code .bash_aliases
    ```

    And add the line `alias r='ranger'` to the file to make this alias permanent.

3. Using ranger for terminal based file explorer: 

    Use the `ranger` command to start the terminal based file browser which provides a splitscreen preview and easily navigate the directories. It provides immediate preview of the file in the splitscreen pane. We can use `Shift + S` to open the terminal in the current directory and open the ranger back using `Ctrl + D`.

    It can be installed using `apt-get` in Linux env.

    ![alt text](assets/image.png)

    ![alt text](assets/image-1.png)

4. Cut long text to arrange the columns properly

    We can use the `tput` command along with `cut` to cut the long running text upto the screen / terminal width.

    ```bash
    btp list accounts/subscription | cut -c 1-$(tput cols)
    ```
    The below image shows text overflowing to the next lines:

    ![alt text](assets/image-2.png)

    It can be formatted easily using the command to show it aligned:

    ![alt text](assets/image-3.png)

5. 

## Curl

## Jq

1. `fromjson` function to read unescape json within json property

   ```json
    {
        "environmentInstances": [
            {
                "id": "C803F6D3-ED1E-4E9D-BB75-81DACEFB6DA0",
                "parameters": "{\"instance_name\":\"2ddfba9btrial\",\"archetype\":\"trial\",\"status\":\"ACTIVE\"}",
                "labels": "{\"API Endpoint\":\"https://api.cf.us10-001.hana.ondemand.com\",\"Org Name\":\"2ddfba9btrial\",\"Org ID\":\"92f63e3d-bcb8-4131-b16f-c74af7d456c5\",\"Org Memory Limit\":\"4,096MB\"}",
                "customLabels": {},
            }
        ]
    }
   ```

    We can use `jq` function `fromjson` to unescape the labels, parameters properties.

    ```jq
    jq '.environmentInstances[0].labels | fromjson' env.json 
    ```

    It gives out the formatted JSON correctly as follows:
    ```JSON
    {
        "API Endpoint": "https://api.cf.us10-001.hana.ondemand.com",
        "Org Name": "2ddfba9btrial",
        "Org ID": "92f63e3d-bcb8-4131-b16f-c74af7d456c5",
        "Org Memory Limit": "4,096MB"
    }
    ```

2. Reading JSON with spaces in the key

    The key name has to be in double-quotes, and we use `--raw-output` or `-r` to remove any quotes in the output.
    ```jq
    > jq '.environmentInstances[0].labels | fromjson."API Endpoint"' env.json -r

    https://api.cf.us10-001.hana.ondemand.com
    ```

## BTP

1. Enable Autocomplete in BTP - useful for performing any operation by pressing TAB.

    ````bash
    btp enable autocomplete bash
    ````

2. Get CF API Endpoint using BTP Cli:

    ```bash
    > btp --format json list accounts/environment-instance 

    {
    "environmentInstances": [
        {
        "id": "C803F6D3-ED1E-4E9D-BB75-81DACEFB6DA0",
        "name": "2ddfba9btrial",
        "brokerId": "985B006B-820E-40BF-AC53-AB3D2B86C294",
        "globalAccountGUID": "9c49735e-6b4e-4689-844a-9a13460fec39",
        "subaccountGUID": "459d0dd0-3b2d-4a74-a0a8-e540eacd17d8",
        "tenantId": "459d0dd0-3b2d-4a74-a0a8-e540eacd17d8",
        "serviceId": "fa31b750-375f-4268-bee1-604811a89fd9",
        "planId": "267b5620-3011-4c48-8e56-8d103876275b",
        "operation": "provision",
        "parameters": "{\"instance_name\":\"2ddfba9btrial\",\"archetype\":\"trial\",\"status\":\"ACTIVE\"}",
        "labels": "{\"API Endpoint\":\"https://api.cf.us10-001.hana.ondemand.com\",\"Org Name\":\"2ddfba9btrial\",\"Org ID\":\"92f63e3d-bcb8-4131-b16f-c74af7d456c5\",\"Org Memory Limit\":\"4,096MB\"}",
        "customLabels": {},
        "type": "Provision",
        "status": "Processed",
        "environmentType": "cloudfoundry",
        "landscapeLabel": "cf-us10-001",
        "platformId": "92f63e3d-bcb8-4131-b16f-c74af7d456c5",
        "createdDate": "Oct 21, 2024, 6:17:14 AM",
        "modifiedDate": "Oct 21, 2024, 6:17:33 AM",
        "state": "OK",
        "stateMessage": "Environment instance created.",
        "serviceName": "cloudfoundry",
        "planName": "trial"
        }
    ]
    }
 
    ```

    We can format the same using Jq to display only the API Endpoint 

    ```bash
    > btp --format json list accounts/environment-instance | jq '.environmentInstances[0].labels | fromjson | ."API Endpoint"' --raw-output

    https://api.cf.us10-001.hana.ondemand.com 
    ```

    Here we use Jq to navigate to the API Endpoint variable.  

## Cloud Foundry

## CDS (Node.js)

1. Add Swagger UI to CAP Project

    ```bash
    npm add --save-dev cds-swagger-ui-express
    ```

    Example Page: [Swagger UI - Petstore](https://petstore.swagger.io/)

2. Generate UUID in CDS

    The below CDS utility function can be used to generate a random UUID using the crypto module of Node.Js. The same function is available in the utility file : `/node_modules/@sap/cds/lib/utils/cds-utils.js`

    ```bash
    > cds.utils.uuid() 

    aec55e7e-b3f1-48ed-a044-76ec96d73ce6
    ```

3. CDS Repl for Interactive Execution

    Using the `.run` command in CDS Repl, the CDS Runtime provides several variables in the global context to interact and play around the execution.

    ```bash
    > .run
    [cds] - loaded model from 2 file(s):

    srv/cat-service.cds
    db/schema.cds

    [cds] - connect to db > sqlite { url: ':memory:' }
    > init from db/data/my.bookshop-Books_Authors.csv 
    > init from db/data/my.bookshop-Books.csv 
    > init from db/data/my.bookshop-Authors.csv 
    /> successfully deployed to in-memory database. 

    [cds] - using auth strategy {
    kind: 'mocked',
    impl: 'node_modules/@sap/cds/lib/srv/middlewares/auth/basic-auth'
    } 

    [cds] - using new OData adapter
    [cds] - serving CatalogService { path: '/odata/v4/catalog' }

    [cds] - server listening on { url: 'http://localhost:39215' }
    [cds] - launched at 1/8/2025, 6:33:25 AM, version: 8.6.0, in: 630.756ms
    [cds] - [ terminate with ^C ]


    ------------------------------------------------------------------------
    Following variables are made available in your repl's global context:

    from cds.entities: {
    Books,
    Authors,
    Books_Authors,
    }

    from cds.services: {
    db,
    CatalogService,
    }

    ```

    We can use the REPL to query the objects using CQL and test the application.

## Node.js

1. List all installed packages globally:

```bash
npm ls -g
npm list --global
```

It would show all the installed packages in tree form.

```bash
/usr/local/share/nvm/versions/node/v20.18.1/lib
├── @sap/cds-dk@8.6.1
├── @sap/generator-base-mta-module@2.0.0
├── @sap/generator-cap-project@1.8.2
├── @sap/generator-fiori@1.16.1
├── @sap/generator-hdb-project@1.14.0
├── @sap/ux-ui5-tooling@1.16.0
├── corepack@0.29.4
├── generate-gitignore@0.2.6
├── generate@0.14.0
├── npm@10.8.2
└── pnpm@9.15.0
```
