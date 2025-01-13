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

3. XSENV and VCAP_SERVICES

    <b>XSENV</b> (SAP XS Environment) helps bridge the gap between local development and runtime environments by mimicking or accessing configurations like those in `VCAP_SERVICES`.

    <b>VCAP_SERVICES</b> (Vendor Capability Services) provides a standardized way for applications to discover and use bound services in Cloud Foundry environments.

    default-env.json

    ```json, filename=default-env.json
    {
        "VCAP_SERVICES": {
            "name1": "value1",
            "name2": "value2",
            "name3": "value3"
        }
    }
    ```

    And we run a `node.js` file as follows:

    ```js
    const xsenv = require('@sap/xsenv');
    xsenv.loadEnv()
    console.log("VCAP_SERVICES:", process.env.VCAP_SERVICES);
    ```
    It would access the file when running in local env and print the file.

    ```bash
    > node server.js
    VCAP_SERVICES: {"name1":"value1","name2":"value2","name3":"value3"}
    ```

4. XSSEC and PASSPORT

    Using these, we ensure that the requests coming to the application are authenticated with the bound `xsuaa` service. 

    The below is the simplest example to demonstrate secure requests and unsecure requests.

    ```js
    const express = require("express");
    const passport = require("passport");
    const xssec = require("@sap/xssec");
    const xsenv = require("@sap/xsenv");
    const JWTStrategy = require("@sap/xssec").v3.JWTStrategy;
    const app = express();
    
    xsenv.loadEnv();
    const xsuaaCredentials = xsenv.serviceCredentials({ tag: "xsuaa" });

    passport.use(new JWTStrategy(xsuaaCredentials));
    app.use(passport.initialize());

    app.get(
    "/secure",
    passport.authenticate("JWT", { session: false }),
    (req, res) => {
        res.json({
        message: "Hello, Secure World!",
        });
    }
    );

    app.get("/", (req, res) => {
    res.send("Hello, World!\n");
    });

    const port = 4005;
    app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
    });

    ```

    The `default-env.json` file is as below containing the service key to `xsuaa` service.
    
    ```json
    {
    "VCAP_SERVICES": {
        "xsuaa": [
        {
            "name": "xssec-demo-xsuaa",
            "instance_name": "xssec-demo-xsuaa",
            "label": "xsuaa",
            "tags": [
            "xsuaa",
            "endpoint:https://api.cf.us10-001.hana.ondemand.com",
            "org:2ddfba9btrial",
            "space:dev"
            ],
            "plan": "application",
            "credentials": {
            "apiurl": "https://api.authentication.us10.hana.ondemand.com",
            "clientid": "<MASKED_CLIENT_ID>",
            "clientsecret": "<MASKED_CLIENT_SECRET",
            "credential-type": "binding-secret",
            "identityzone": "2ddfba9btrial",
            "identityzoneid": "459d0dd0-3b2d-4a74-a0a8-e540eacd17d8",
            "sburl": "https://internal-xsuaa.authentication.us10.hana.ondemand.com",
            "serviceInstanceId": "cbb83a4b-a6da-46ec-a8c9-3f3f374df967",
            "service_key_name": "key",
            "subaccountid": "459d0dd0-3b2d-4a74-a0a8-e540eacd17d8",
            "tenantid": "459d0dd0-3b2d-4a74-a0a8-e540eacd17d8",
            "tenantmode": "shared",
            "uaadomain": "authentication.us10.hana.ondemand.com",
            "url": "https://2ddfba9btrial.authentication.us10.hana.ondemand.com",
            "verificationkey": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAjCyFKyzVKR0ak1j1EmMm\nwIZekrAHtjQ6p22Gr+mO2BBRPCgOzlzpQ/7/xyg1B+kx6CoCfwT3EyKl/UZUfmTP\n3+SL4WGmTyyH7DJA4qw0lZcbdnu0uclZImjkNDILqdedqpUItS4Vt9MqX/tbfSCJ\nCcC2oNZrfmj9OcuAjYZtfa+lxxS4a1RqrbJrudkWBVuK92Euilw913eONn6cB1WE\nuc7+RyNS5cz4IxaYGZvExbYgY7yro4DX16ZRdgtEaZgeJD8bD1ngAHVlZoWWXtqK\nU/13EsK5XIyEMJ0Efu47qoOx6B2EYkoa/npkjE+RBRcTMh+GMc1nXjwvXYw3h8Gn\nCwIDAQAB\n-----END PUBLIC KEY-----",
            "xsappname": "na-a39ee602-060a-4e63-b4f5-6ea4ebfad586!t355257",
            "zoneid": "459d0dd0-3b2d-4a74-a0a8-e540eacd17d8"
            }
        }
        ]
    }
    }

    ```

    Using `xsenv` the program loads this file into the `node.js` server. The program is able to run the unsecure request but responds with a `401: Unauthorised` response for the `/secure` endpoint.

    ```bash
    > node server.js

    Server is running on port 4005
    ```

    Curl Requests

    ```bash
    > curl -si 'localhost:4005'

    HTTP/1.1 200 OK
    X-Powered-By: Express
    Content-Type: text/html; charset=utf-8
    Content-Length: 14
    ETag: W/"e-YP3pwjELDUytTauNEmsEOH77ook"
    Date: Mon, 13 Jan 2025 15:31:13 GMT
    Connection: keep-alive
    Keep-Alive: timeout=5

    Hello, World!

    > curl -si 'localhost:4005/secure'

    HTTP/1.1 401 Unauthorized
    X-Powered-By: Express
    Date: Mon, 13 Jan 2025 15:31:41 GMT
    Connection: keep-alive
    Keep-Alive: timeout=5
    Content-Length: 12

    Unauthorized
    ```

    To connect to the `/secure` endpoint, we need to get the access-token from the XSUAA Service. We use two different ways here: `client_credentials` and `pasword` grant-types.

    <b> 4.1 Client Credentials </b>
    
    We use `curl` to get the access-token and call the `/secure` endpoint.

    ```bash
    > curl -s -L --request POST 'https://2ddfba9btrial.authentication.us10.hana.ondemand.com/oauth/token' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --user '<ClientID>:<ClientSecret>' \
    --data 'grant_type=client_credentials'

    {
        "access_token": "ey...",
        "token_type": "bearer",
        "expires_in": 43199,
        "scope": "uaa.resource",
        "jti": "395a2af65c7e48e8819cad0f53ca2e74"
    }

    >  curl -s 'localhost:4005/secure' --header 'Authorization: Bearer ey...' | jq .

    {
        "message": "Hello, Secure World!"
    }
    ```

    However, as this is not authenticated using the User Credentials, we don't get the user details in the token as well as the `req` variable when calling the `/secure` endpoint.

    <b> 4.2 Password </b>
    
    We use `curl` to get the access-token and call the `/secure` endpoint.

    ```bash
    > curl -s -L --request POST 'https://2ddfba9btrial.authentication.us10.hana.ondemand.com/oauth/token' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --user '<ClientID>:<ClientSecret>' \
    --data 'grant_type=password'
    --data '<emailID' 
    --data '<Password>'

    {
        "access_token": "ey..",
        "token_type": "bearer",
        "id_token": "ey..",
        "refresh_token": "6040f252810b4596b0d2ec56127f7db5-r",
        "expires_in": 43199,
        "scope": "openid",
        "jti": "20438325045a479bae0d60acc06cc1a2"
    }


    >  curl -s 'localhost:4005/secure' --header 'Authorization: Bearer ey...' | jq .

    {
        "message": "Hello, Secure World!"
    }
    ```

    This is user authenticated request and we get the user details in the token as well as the `req` variable when calling the `/secure` endpoint.

    `req.user`

    ```json
    {
        "id": "<emailID>", 
        "name": {
            "givenName": "Ashutosh", 
            "familyName": "Joshi"}, 
            "emails": 
                    [{
                        "value": "<emailID>"
                    }]
    }
    ```


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

## Fiori / UI5

1. Understanding the standard controls

    To understand what's happening under the hood, it's useful to see the implementation of the standard controls like `sap.m.Text` for example. We can see them in console when the page is loaded, however it's a minified version (preload version). To see the complete implementation we use the openui5 git repo.
    Some of the files like `sap.ui.comp` is not available because its not open-source and can be checked only via the CDN and not GitRepo for Openui5.

    ```
    https://github.com/SAP/openui5/

    https://ui5.sap.com/resources/sap/ui/comp/odata/MetadataAnalyser-dbg.js   (Annotation Processing)

    https://ui5.sap.com/resources/sap/ui/comp/smarttable/SmartTable-dbg.js    (SmartTable Control)

    https://sapui5.hana.ondemand.com/1.131.1/resources/sap/fe/core/converters/ManifestSettings.ts  (Std. List Report Manifest Settings)

    https://sapui5.hana.ondemand.com/1.131.1/resources/sap/fe/core/controllerextensions/EditFlow.ts   (List Report Template EditFlow)

    https://sapui5.hana.ondemand.com/1.131.1/resources/sap/fe/templates/ListReport/ListReportController.controller.ts (List Report Controller)
    ```

2. Display the non-minified version of the files

    As a developer, we don't like to work with the minified version of the controller files. They hide the crucial elements and make it difficult to understand the code behind the app. We can use the parameter `sap-ui-debug=true` in the URL to requst the `dbg` files which are the non-minified versions and makes it easy for the developer to debug, understand and troubleshoot any application.

    ```
    https://your-app-url?sap-ui-debug=true
    ```

3. Types of Bindings

    Property Binding: Binds a single property of a control (e.g., `value` of an `Input`).

    ```xml
    <Input value="{/Name}" />
    ```

    Element Binding: Binds an entire object to a control and its children.

    ```xml
    <Panel binding="{/Employee}">
        <Text text="{Name}" />
        <Text text="{Position}" />
    </Panel>
    ```

    Aggregation Binding: Binds a collection of data to aggregation controls like Table, List, or ComboBox.

    ```xml
    <Table items="{/Employees}">
        <columns>
            <Column>
                <Text text="Name" />
            </Column>
        </columns>
        <items>
            <ColumnListItem>
                <Text text="{Name}" />
            </ColumnListItem>
        </items>
    </Table>
    ```

4. OData V4 Model

    Below commands can be used to play around and dig through the Model in Chrome Developer Tools:

    ```Js
    //List Report Example
    oTable = sap.ui.getCore().byId("my.bookshop::BooksList--fe::table::Books::LineItem::Table")
    oModel = oTable.getModel();
    
    //Reading the Metadata and Service Document
    oData = oModel.getMetaModel().getData();
    {
        "$Version": "4.0",
        "$Reference": {
            ...
        },
        "CatalogService.": {
            ...
        },
        "CatalogService.EntityContainer": {
            "$kind": "EntityContainer",
            "Books": {
                ...
            }
        },
        "$EntityContainer": "CatalogService.EntityContainer",
        "CatalogService.Books": {
            ...
        },
        "$Annotations": {
            ...
        }
    }

    // Fetching the Properties in an Entity
    oBooks = oData[`CatalogService.Books`]
    Object.keys(oBooks).filter( (key) => oBooks[`${key}`].$kind == 'Property' )

    ["ID","title","stock"]
     
    // Making OData Calls from Console in OData V4 Model. 
    await oModel.bindContext("/Books('2')").requestObject();

    // ListBinding
    oList = oModel.bindList("/Books")
    /*
    getContexts - Retrieves the visible rows or items in the table or list.
    requestContexts - Asynchronously fetches all contexts for the bound entity set.
    filter - Applies client-side or server-side filters to the binding. 
    */
    oBook1 = oList.getContexts(0,1)[0]
    oBook1.setProperty('stock',200)

    // ContextBining - https://sapui5.hana.ondemand.com/1.131.1/resources/sap/ui/model/odata/v4/ODataContextBinding-dbg.js
    oContextBinding = oMode.bindContext('/Books')
    oContextBinding.getBoundContext().setProperty('stock',15) 
    oContextBinding.getBoundContext().delete()

    // Accessing the Table Rows 
    oRowBinding = oTable.getRowBinding()  // This gives ODataListBinding which can be  used with getContexts to access each row
    oContext = oRowBinding.getContexts().[0]
    oContext.setSelectec(true)
    

    // Back and Reload using
    window.history.back()
    window.location.reload()
    ```

5. Expression Parsing in UI5

    ```url
    https://sapui5.hana.ondemand.com/1.131.1/resources/sap/ui/base/ExpressionParser-dbg.js
    ```

    This file shows how the parsing works for Binding expressions like below

    ```xml
    <Text text="{parts: [{path: 'FirstName'}, {path: 'LastName'}], formatter: '.formatName'}" />
    ```

6. Fiori Element Templates

    These are metadata driven templates like List Report template, Analytical Page, Object Page. They are controlled using the Annotations embedded in the metadata.

    ```url
    Metadata Processing: sap.fe.core.AnnotationHelper
    File: sap.fe.templates.ListReport.ListReportController
    Template Processing: https://sapui5.hana.ondemand.com/1.131.1/resources/sap/fe/core/converters/TemplateConverter.ts 
    ```
