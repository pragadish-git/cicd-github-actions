<a name="readme-top"></a>
# ncau-icoe-integration-boomi-common


## Boomi CICD Workflow

This document discusses the Boomi CICD reference implementation and provides technical overview and examples for how to use Boomi platform with Github for CICD. The Boomi platform offers a rich set of APIs that can be used by clients to automate actions such as runtime installation, component deployment, monitoring, and more. Leveraging these APIs, we have built CI/CD implementations to enhance software delivery by automating the packaging, deployment and updating environments extensions.

 <p align="center"><img src="./images/Release%20Pipeline%20Process.png" /></p>
 <p/>

## Process Flow

  1.  Create a new branch from master of github repository https://github.com/newscorp-ghfb/ncau-icoe-integration-boomi-common.
        
      <p align="center"><img src="./images/Create%20New%20Branch%20From%20Master.jpg" /></p>

        **NOTE** - Create the branch name with prefix develop-* (example: **develop-release-3.4**)

  2.  Push the release details as json files into the new develop-* branch.

      **NOTE** - If there are exisiting release files, modify the files and push the new changes.
      
      <p align="center"><img src="./images/New%20Branch%20with%20Release%20files.jpg" /></p>
      
      - package.json contains the details of all deployable components impacted or developed with respect to the change/fix. These would include top-level processes, API components, and process routes for instance, but no sub-processes should be included here. 
        
            ## Example packages.json
            
            [
              {
                "componentId": "6ef02f50-9ff7-44e5-96e0-cd1ffbde9be4",
                "componentName": "[WSS] Prospect Tracking",
                "componentType": "process",
                "packageVersion": "3.1"
              },
              {
                "componentId": "19d4cfcb-2978-4374-9d3d-987b350c10af",
                "componentName": "[Event] Prospect Tracking",
                "componentType": "process",
                "packageVersion": "3.5"
              }
            ]
        
          - **componentId** → Unique identifier of the component. String value (valid component ID)
          - **componentName** → Custom name defined for the component
          - **componentType** → certificate, customlibrary, flowservice, process, processroute, tpgroup, or webservice
          - **packageVersion** → Any string value (valid package version)
          <p align="right">(<a href="#readme-top">back to top</a>)</p>
      
      - extension.json contains the details of all the extended connection fields and its value. Example extension.json below

            ## Example extensions.json
            
            [
              {
                "type": "connection",
                "connectionId": "eb70feac-860a-43db-ab6f-4944967a233e",
                "fieldId": "host",
                "fieldValue": "$BT_MYSQL_HOST",
                "encrypt": false
              },
              {
                "type": "connection",
                "connectionId": "eb70feac-860a-43db-ab6f-4944967a233e",
                "fieldId": "port",
                "fieldValue": "$BT_MYSQL_PORT",
                "encrypt": false
              },
              {
                "type": "connection",
                "connectionId": "eb70feac-860a-43db-ab6f-4944967a233e",
                "fieldId": "username",
                "fieldValue": "$BT_MYSQL_USER",
                "encrypt": false
              },
              {
                "type": "connection",
                "connectionId": "eb70feac-860a-43db-ab6f-4944967a233e",
                "fieldId": "password",
                "fieldValue": "$BT_MYSQL_PWD",
                "encrypt": true
              },
              {
                "type": "connection",
                "name": "9fa3a7be-87a6-422b-950d-acb40ca90d20",
                "id": "url",
                "value": "$BT_SF_URL",
                "encrypt": false
              },
              {
                "type": "connection",
                "name": "9fa3a7be-87a6-422b-950d-acb40ca90d20",
                "id": "user",
                "value": "$BT_SF_USER",
                "encrypt": false
              },
              {
                "type": "connection",
                "name": "9fa3a7be-87a6-422b-950d-acb40ca90d20",
                "id": "password",
                "value": "$BT_SF_PWD",
                "encrypt": true
              }
            ]
        
          - **type** → type of environment extension
          - **connectionId** → Unique identifier of the component. String value (valid component ID) 
          - **fieldId** → exact name of the connection component’s field name
          - **fieldValue** → value of the connection components’s field
          - **encrypt** → boolean to encrypt fields such as passwords
           
          <br/>
          **NOTE** : The value for respective extension connection fields have to be created as environment variables in github actions. Navigate to Setting → Environments → <**Environment**> → Environment Secrets → Add Secret

          <p align="center"><img src="./images/Environment%20Variables.jpg" /></p>
          
          <p align="right">(<a href="#readme-top">back to top</a>)</p>

  3.  Github action will auto trigger the CICD pipeline on a push event on develop-* branch
        
      <p align="center"><img src="./images/Github%20Actions.jpg" /></p>

      Build action will package all the components mentioned in the package.json.
        
      <p align="center"><img src="./images/Build%20Action.jpg" /></p>
      <p align="right">(<a href="#readme-top">back to top</a>)</p>

  4.  Select the environment for deployment and Click on “Run workflow”. Run workflow on an environment will trigger the deployment of packaged components followed by extension updation. Extensions are optional and it will be triggered only on extension.json check in. 
  
      Navigate to Actions → Workflows → Boomi CICD Workflow → Run workflow  
  
      Deployements are performed by below order
      - Run workflow on **Development**
        <p align="center"><img src="./images/Run%20workflow%20on%20Dev.jpg" /></p>

        - Status of workflow execution
          <p align="center"><img src="./images/Deploy%20to%20Dev%20-%20Status.jpg" /></p>

      - Run workflow on **SIT**
        <p align="center"><img src="./images/Run%20workflow%20on%20SIT.jpg" /></p>

        - Status of workflow execution
          <p align="center"><img src="./images/Deploy%20to%20SIT%20-%20Status.jpg" /></p>

      - Run workflow on **UAT**
        <p align="center"><img src="./images/Run%20workflow%20on%20UAT.jpg" /></p>

        - Status of workflow execution
          <p align="center"><img src="./images/Deploy%20to%20UAT%20-%20Status.jpg" /></p>

        <p align="right">(<a href="#readme-top">back to top</a>)</p>

  5.  Deployment on Production environment will be auto initated on merge request to master.
      
      - After successful deployment and testing on previous environments, user should initate a pull request to merge develop-* branch into master using **Compare & pull request** option

      <p align="center"><img src="./images/Create%20Pull%20Request%20For%20Master.jpg" /></p>
      <p align="right">(<a href="#readme-top">back to top</a>)</p>

      - Details of merge will be shown to the user

      <p align="center"><img src="./images/Merge%20Confirmation%20to%20Master.jpg" /></p>
      <p align="right">(<a href="#readme-top">back to top</a>)</p>

      - Confirm the changes to complete the merge

      <p align="center"><img src="./images/Confirm%20Pull%20Request%20For%20Master.jpg" /></p>
      <p align="right">(<a href="#readme-top">back to top</a>)</p>

      - Github action for production deployment will be auto triggered

      <p align="center"><img src="./images/Github%20Actions%20-%20All%20workflows.jpg" /></p>
      <p align="right">(<a href="#readme-top">back to top</a>)</p>

        - Status of workflow execution
          <p align="center"><img src="./images/Deploy%20to%20Prod%20-%20Status.jpg" /></p>
