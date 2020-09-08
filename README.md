
# Transact.Integration.NintexWorkflowCloud

This repository holds information on how to integrate Ephesoft Transact with Nintex Workflow Cloud (NWC). There are two primary ways to integrate Ephesoft and NWC: **Nintex Xtensions** and **External Start**.

## Table of Contents

- [Nintex Xtensions](#nintex-xtensions)
- [External Start](#external-start)

## Prerequisites

To take full advantage of this repository, you will need the following:
- A Nintex Workflow Cloud account

# Nintex Xtensions

Nintex Xtensions is an extensibility framework for Nintex Workflow Cloud, which allows you to create custom connectors to third-party platforms in your workflows. Using this method, Xtensions are used to communicate with the Transact Rest API.

For detailed steps, refer to [Integrating Transact Web Services with Nintex](https://ephesoft.com/docs/products/transact/developers/web-services-api/web-services-integrating-nintex/)

# External Start

The External Start option uses an NWC component workflow to trigger a workflow process.
A Transact [export script](External Start Export Script/ExportScript.java) is used with the Transact **Custom Export Plugin** to format the JSON response that is used to trigger the NWC external start.

## Configuratrating NWC Component workflow, Custom Export plugin, and Export Script.java
1) The NWC workflow start event must be configured to use the component workflow trigger, see the below screenshot for an example of the configuration.
![customconnector Image](/screenshots/2.png)  

2) Configure the needed variables in the NWC Start event that you wish to capture in the JSON payload, the below screenshot shows the example variables. 
![customconnector Image](/screenshots/3.png) 

3) Publish the workflow to get **URL with token**  and the **Request body example** this information will be needed to configure the Transact Export Script and the **Custom Export Plugin** within the Ephesoft Transact batch class. 
![customconnector Image](/screenshots/4.png) 

4)  Moving over to the Transact application, add the following two plugins to the batch classes export module **CUSTOM_EXPORT_PLUGIN** and **EXPORT_SCRIPTING_PLUGIN**
> **Note:** the order of the plugins is important. The CUSTOM_EXPORT_PLUGIN needs to execute before the EXPORT_SCRIPTING_PLUGIN.
5) Configure the **CUSTOM_EXPORT_PLUGIN** for each document type you wish to export from the batch class to the NWC workflow. This will be the JSON payload that will be submitted to the workflow component start event. 
    - Use the information in the [Custom Export Plugin Product documentation](https://ephesoft.com/docs/products/transact/features-and-functions/administrator/moduleplugin-configuration/export-module/custom-export-plugin-configuration-and-user-guide/) to build your custom JSON object
    - **Important** you must use the following configuration for the **File Name** for the Export Metadata File.
    - **\~BI:Identifier\~-\~DOC:Identifier\~** The Export Script we use will use this naming convention. 
  
  ![customconnector Image](/screenshots/1.png)
  
 **Note:**  Below is an example of a JSON payload that can be used in the **Custom Export Plugin**
  
		  {
		    "startData": {
		        "se_test_2": "~BI:Identifier~",
		        "se_test_field": "~DOC:Type~"
				}
			}

6) Now we will configure the Transact Export Script. 

		//This is the path that the custom Export files will be exported to. This script will use the data populated in this configured folder
		public static String CUSTOM_EXPORT_FILE_PATH = "C:\\Ephesoft\\SharedFolders\\final-drop-folder\\CustomExport\\";
> **Note:** The Folder path in the EXPORT_SCRIPT must match with the CUSTOM_EXPORT_PLUGIN Folder Path from step 5. The path should have \\\ for java to escape special characters
7) In this step, we will add Ephesoft document types that should be mapped to any webhooks that are needed to be triggered

		      DOCTYPE_WEBHOOK_MAPPING.put("doctypeName1", "https://XXXXXX.workflowcloud.com/api/v1/workflow/published/tokenxxxxxxxxxxxxxxx");
			DOCTYPE_WEBHOOK_MAPPING.put("doctypeName2", "https://XXXXXX.workflowcloud.com/api/v1/workflow/published/tokenxxxxxxxxxxxxxxx");
>**Note:** copy each line above if you need to support more then one document type.

## Installation
1) Move the ScriptExport.java and be sure to name the file **ScriptExport.java** file to the Scripts folder for your chosen batch class, at [Ephesoft_Directory]\Shared-Folders\<your batch class>\Scripts.

## License

Ephesoft Labs is licensed under the [Ephesoft Source Code License](https://github.com/Ephesoft-Labs/Transact.Integration.NintexWorkflowCloud/blob/master/LICENSE.md).