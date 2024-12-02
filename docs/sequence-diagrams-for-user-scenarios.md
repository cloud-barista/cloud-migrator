
# Computing infra migration scenarios

The sequence diagrams represent user scenarios for computing infra migration.

> [!NOTE]
> 원활한 Cloud-Migrator v0.3.0 통합 및 릴리스를 위해 Sequence Diagram을 작성하였습니다.
> 그 동안 협의된 사항과 사용자 시나리오를 바탕으로 작성되었습니다.

> [!TIP]
> 수정, 보완이 필요한 사항들에 대해 많은 의견을 부탁드립니다.
> 제안) 수정/보완 사항을 PR로 오픈하고 논의하면 좋을 것 같습니다. 🙌


## Login

: Participants: Butterfly

```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant WebServer as "Butterfly"

    %% Step 1: Access the Cloud-Migrator Web Console
    User->>Browser: Access the Cloud-Migrator Web Console
    activate Browser
    Browser->>WebServer: Request a view to login
    activate WebServer
    WebServer-->>Browser: Return the view to login
    deactivate WebServer
    deactivate Browser

    %% Step 2: Enter login credentials
    User->>Browser: Enter login credentials
    activate Browser
    Browser->>WebServer: Send login credentials (Username, Password)
    activate WebServer
    WebServer-->>Browser: Validate and return dashboard page
    deactivate WebServer
    Browser-->>User: Display dashboard page
    deactivate Browser
```


## Register the source computing environment 

: Participants: Butterfly, Honeybee, Source computing environment

: Computing environment includes computing infrastructure, software, data
: Currently, we're concentrating on the compuing infrasturcture.

> [!IMPORTANT] 
> Question) Honeybee README에서 한번에 하나의 서버에 대한 Connection Info를 등록하는 예시를 확인할 수 있습니다. 한번에 여러 서버들의 Connection Info를 등록할 수 있는 방법이 있는지요?

```mermaid
sequenceDiagram
    participant User as User
    participant WebConsole as Web Console
    participant Butterfly as Butterfly
    participant Honeybee as Honeybee
    participant SrcEnv as Source Computing Environment

    %% Step 0: User accesses Source Computing Environment Management View
    User->>WebConsole: Access the view of Source Computing Environment Management
    activate WebConsole
    WebConsole->>Butterfly: Request the Source Computing Environment Management view
    activate Butterfly
    Butterfly-->>WebConsole: Respond with the view
    deactivate Butterfly
    WebConsole-->>User: Display Source Environment Management Interface
    deactivate WebConsole

    %% Step 1: Register Source Group
    User->>WebConsole: Register source group
    activate WebConsole
    WebConsole->>Butterfly: Send request to register source group
    activate Butterfly
    Butterfly->>Honeybee: API call to POST /honeybee/source_group
    activate Honeybee
    Honeybee-->>Butterfly: Return the source group ID (sgID)
    deactivate Honeybee
    Butterfly-->>WebConsole: Respond with Source Group Registration Confirmation (view/message)
    deactivate Butterfly
    WebConsole-->>User: Show source group registration confirmation
    deactivate WebConsole

    %% Step 2: Register Connection Info
    User->>WebConsole: Register connection info with sgID
    activate WebConsole
    WebConsole->>Butterfly: Send request to register connection info with sgID
    activate Butterfly
    Butterfly->>Honeybee: API call to POST /honeybee/source_group/{sgID}/connection_info
    activate Honeybee
    Honeybee->>SrcEnv: Install agent on the user infra
    activate SrcEnv
    SrcEnv-->>Honeybee: Return agent installation status
    deactivate SrcEnv
    Honeybee-->>Butterfly: Return the registered connection information
    deactivate Honeybee
    Butterfly-->>WebConsole: Respond with connection info and status (view/message)
    deactivate Butterfly
    WebConsole-->>User: Display connection registration and status
    deactivate WebConsole

```


## Extract information from the source computing environment

: Participants: Butterfly, Honeybee, Source computing environment

> [!IMPORTANT] 
> Question) `GET /honeybee/source_group/{sgID}/import/infra`에서 import 용어 변경/개선이 필요해 보여 의견을 남겨놓습니다. 

```mermaid
sequenceDiagram
    participant User as User
    participant WebConsole as Web Console
    participant Butterfly as Butterfly
    participant Honeybee as Honeybee
    participant SrcEnv as Source Computing Environment

    %% Step 0: User accesses Source Computing Environment Management View
    User->>WebConsole: Access the view of Source Computing Environment Management
    activate WebConsole
    WebConsole->>Butterfly: Request the Source Computing Environment Management view
    activate Butterfly
    Butterfly-->>WebConsole: Respond with the view
    deactivate Butterfly
    WebConsole-->>User: Display Source Environment Management Interface
    deactivate WebConsole

    %% Step 1: Extract Source Info
    User->>WebConsole: Save current source information
    activate WebConsole
    WebConsole->>Butterfly: Send request to save source information
    activate Butterfly
    Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgID}/import/infra
    activate Honeybee
    Honeybee->>SrcEnv: Extract information of the source computing environment
    activate SrcEnv
    SrcEnv-->>Honeybee: Return the extracted information
    deactivate SrcEnv
    Honeybee-->>Butterfly: Return the saved the infra information
    deactivate Honeybee
    Butterfly-->>WebConsole: Respond with the saved the infra information (view/message)
    deactivate Butterfly
    WebConsole-->User: Display save confirmation
    deactivate WebConsole

```


## Retrieve the information of source computing environment (raw data, shape information)

: Participants: Butterfly, Honeybee

```mermaid
sequenceDiagram
    participant User as User
    participant WebConsole as Web Console
    participant Butterfly as Butterfly
    participant Honeybee as Honeybee

    %% Step 0: User accesses Source Computing Environment Management View
    User->>WebConsole: Access the Source Computing Environment Management view
    activate WebConsole
    WebConsole->>Butterfly: Request the Source Computing Environment Management view
    activate Butterfly
    Butterfly-->>WebConsole: Respond with the view
    deactivate Butterfly
    WebConsole-->>User: Display Source Environment Management Interface
    deactivate WebConsole
    
    %% Step 1: Get Saved Source Info
    User->>WebConsole: Retrieve saved source information
    activate WebConsole
    WebConsole->>Butterfly: Request to retrieve saved source information
    activate Butterfly
    Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgID}/infra
    activate Honeybee
    Honeybee-->>Butterfly: Return saved infrastructure data
    deactivate Honeybee
    Butterfly-->>WebConsole: Respond with the saved infrastructure data (raw data)
    deactivate Butterfly
    WebConsole-->User: Display infrastructure data in JSON editor
    deactivate WebConsole

```


## Retrieve the information of source computing environment (refined data, onpremise model)

: Participants: Butterfly, Honeybee, Damselfly

```mermaid
sequenceDiagram
    participant User as User
    participant Browser as Web Console
    participant Butterfly as Butterfly
    participant Honeybee as Honeybee
    participant Damselfly as Damselfly

    %% Step 0: User accesses Source Computing Environment Management View
    User->>Browser: Access the Source Computing Environment Management view
    activate Browser
    Browser->>Butterfly: Request the Source Computing Environment Management view
    activate Butterfly
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Source Environment Management Interface
    deactivate Browser
    
    %% Step 1: Get Saved Source Info
    User->>Browser: Retrieve saved source information
    activate Browser
    Browser->>Butterfly: Request to retrieve saved source information
    activate Butterfly
    Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgID}/infra/refined
    activate Honeybee
    Honeybee-->>Butterfly: Return saved infrastructure data
    deactivate Honeybee
    Butterfly-->>Browser: Respond with the saved infrastructure data (refined data)
    deactivate Butterfly
    Browser-->>User: Display infrastructure data in JSON editor
    deactivate Browser

    %% Optional Step: Save the original onpremmodel
    opt Save the original onpremmodel
        User->>Browser: Save original onpremmodel
        activate Browser
        Browser->>Butterfly: Request to save onpremmodel
        activate Butterfly
        Butterfly->>Damselfly: POST /damselfly/onpremmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the saved model information
        deactivate Damselfly
        Butterfly-->>Browser: Respond with the save confirmation
        deactivate Butterfly
        Browser-->>User: Display the confirmation
        deactivate Browser
    end

```


## Register or modify user's source model

: Participants: Butterfly, Damselfly, Honeybee

> [!IMPORTANT]
> Opinion) 모델 관련 UI 구성과 이를 위해 필요한 API 목록, 호출 순서, 호출 시점 등에 대해 논의/정리가 필요해 보입니다.

```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant Butterfly as "Butterfly"
    participant Damselfly as "Damselfly"
    participant Honeybee as "Honeybee"
	
	%% Step 0: User accesses Migration Model Management View
    User->>Browser: Access the Migration Model Management view
    activate Browser
    Browser->>Butterfly: Request the Migration Model Management view
    activate Butterfly
    opt Retrieve the source model from Honeybee
        Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgId}/infra/refined
        activate Honeybee
        Honeybee-->>Butterfly: Return the onpremise infra model (refined data)
        deactivate Honeybee
    end    
    opt Retrieve the source models (onpremise model) from Damselfly
        Butterfly->>Damselfly: API call to GET /damselfly/onpremmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the onpremise model list
        deactivate Damselfly
    end
    opt Retrieve the target models (cloud model) from Damselfly        
        Butterfly->>Damselfly: API call to GET /damselfly/cloudmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the cloud model list
        deactivate Damselfly
    end
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Migration Model Management Interface
    deactivate Browser

    %% Save the origianl onpremmodel
    User->>Browser: Save original onpremmodel request
    activate Browser
    Browser->>Butterfly: Request to save onpremmodel
    activate Butterfly
    Butterfly->>Damselfly: API call to POST /damselfly/onpremmodel
    activate Damselfly
    Damselfly-->>Butterfly: Return the saved model information
    deactivate Damselfly
    Butterfly-->>Browser: Respond with the save confirmation
    deactivate Butterfly
    Browser-->>User: Display the confirmation
    deactivate Browser

    %% Modify the model on JSON editor and save the model
    User->>Browser: Modify onpremmodel in JSON editor and save it
    activate Browser
    Browser->>Butterfly: Request to save modified onpremmodel
    activate Butterfly
    Butterfly->>Damselfly: API call to PUT /damselfly/onpremmodel (modified data)
    activate Damselfly
    Damselfly-->>Butterfly: Return saved model information (version specified)
    deactivate Damselfly
    Butterfly-->>Browser: Respond with the modification confirmation
    deactivate Butterfly
    Browser-->>User: Display the confirmation
    deactivate Browser


```


## Recommend the target computing infrastructure

: Participants: Butterfly, Damselfly, Beetle, Tumblebug

```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant Butterfly as "Butterfly"
    participant Damselfly as "Damselfly"
    participant Beetle as "Beetle"
    participant Tumblebug as "Tumblebug"
	
	%% Step 0: User accesses Migration Model Management View
    User->>Browser: Access the Migration Model Management view
    activate Browser
    Browser->>Butterfly: Request the Migration Model Management view
    activate Butterfly  
    opt Retrieve the source models from Damselfly
        Butterfly->>Damselfly: API call GET /damselfly/onpremmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the onpremise model list
        deactivate Damselfly
    end

	%% Retrieve the registered providers and regions
	Butterfly->>Tumblebug: API call GET /tumblebug/provider
	activate Tumblebug
	Tumblebug-->>Butterfly: List all registered providers
	deactivate Tumblebug
	Butterfly->>Tumblebug: API call GET /provider/{providerName}/region
	activate Tumblebug
	Tumblebug-->>Butterfly: List all registered region info
	deactivate Tumblebug
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Migration Model Management Interface
    deactivate Browser

    %% Recommend target cloud infrastructure
    User->>Browser: Select a source model, provier, and region
    activate Browser    
    Browser->>Butterfly: Request to recommend target infrastructure
    activate Butterfly
    Butterfly->>Beetle: API call POST /beetle/recommendation/mci
    activate Beetle     
    Beetle->>Tumblebug: API call POST /tumblebug/mciRecommendVm
    activate Tumblebug
    Tumblebug-->>Beetle: Return the recommended VM spec list based on filtering and prioritizing
    deactivate Tumblebug
    Beetle->>Tumblebug: API call POST /tumblebug/mciDynamicCheckRequest
    activate Tumblebug
    Tumblebug-->>Beetle: Return the available VM spec and OS image
    deactivate Tumblebug
    Beetle->>Beetle: Evaluate the infra similarity between source and target
    Beetle-->>Butterfly: Return the recommended target infrastructure as a target model
    deactivate Beetle
     
    %% Optional Step: Save the original target model (cloud model)
    opt Save the original target model (cloud model)
        User->>Browser: Save original cloudmodel
        activate Browser
        Browser->>Butterfly: Request to save cloudmodel
        activate Butterfly
        Butterfly->>Damselfly: POST /damselfly/cloudmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the saved model information
        deactivate Damselfly
        Butterfly-->>Browser: Respond with the save confirmation
        deactivate Butterfly
        Browser-->>User: Display the confirmation
        deactivate Browser
    end


```


## Register or modify user's target model

: Participants: Butterfly, Damselfly

```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant Butterfly as "Butterfly"
    participant Damselfly as "Damselfly"
	
	%% Step 0: User accesses Migration Model Management View
    User->>Browser: Access the Migration Model Management view
    activate Browser
    Browser->>Butterfly: Request the Migration Model Management view
    activate Butterfly
    opt Retrieve the source models (onpremise model) from Damselfly
        Butterfly->>Damselfly: API call to GET /damselfly/onpremmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the onpremise model list
        deactivate Damselfly
    end
    opt Retrieve the target models (cloud model) from Damselfly        
        Butterfly->>Damselfly: API call to GET /damselfly/cloudmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the cloud model list
        deactivate Damselfly
    end		
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Migration Model Management Interface
    deactivate Browser

    %% Save the origianl target model (cloud model)
    User->>Browser: Save original cloud model request
    activate Browser
    Browser->>Butterfly: Request to save onpremmodel
    activate Butterfly
    Butterfly->>Damselfly: API call to POST /damselfly/cloudmodel
    activate Damselfly
    Damselfly-->>Butterfly: Return the saved model information
    deactivate Damselfly
    Butterfly-->>Browser: Respond with the save confirmation
    deactivate Butterfly
    Browser-->>User: Display the confirmation
    deactivate Browser

    %% Modify the model on JSON editor and save the model
    User->>Browser: Modify cloudmodel in JSON editor and save it
    activate Browser
    Browser->>Butterfly: Request to save modified cloudmodel
    activate Butterfly
    Butterfly->>Damselfly: API call to PUT /damselfly/cloudmodel (modified data)
    activate Damselfly
    Damselfly-->>Butterfly: Return saved model information (version specified)
    deactivate Damselfly
    Butterfly-->>Browser: Respond with the modification confirmation
    deactivate Butterfly
    Browser-->>User: Display the confirmation
    deactivate Browser


```


## Migrate to cloud infrastructure

: Participants: Butterfly, Damselfly, Beetle, Tumblebug

```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant Butterfly as "Butterfly"
    participant Damselfly as "Damselfly"
    participant Beetle as "Beetle"
    participant Tumblebug as "Tumblebug"
	
	%% Step 0: User accesses Migration Model Management View
    User->>Browser: Access the Migration Model Management view
    activate Browser
    Browser->>Butterfly: Request the Migration Model Management view
    activate Butterfly
    opt Retrieve the target models (cloud model) from Damselfly        
        Butterfly->>Damselfly: API call to GET /damselfly/cloudmodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the cloud model list
        deactivate Damselfly
    end		
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Migration Model Management Interface
    deactivate Browser

    %% Select a target model (cloud model)
    User->>Browser: Select a target model
    activate Browser
    User->>Browser: Execute migration to cloud infrastructure
    Browser->>Butterfly: Request to migrate to cloud infrastructure
    activate Butterfly
    Butterfly->>Beetle: API call to POST /beetle/migration/ns/{nsId}/mci
    activate Beetle
    Beetle->>Tumblebug: API call to POST /tumblebug/ns/{nsId}/mciDynamic
    activate Tumblebug
    Tumblebug-->>Beetle: Return the migrated infra info
    deactivate Tumblebug
    Beetle-->>Butterfly: Return the migrated infra info
    deactivate Beetle
    Butterfly-->>Browser: Respond with the migrated infra info
    deactivate Butterfly
    Browser-->>User: Display the the migrated infra info
    deactivate Browser

```

## Load computing infra migration workflow template and make/create a workflow 

: Participants: Butterfly, Damselfly, Cicada

> [!NOTE] 
> * 다음은 몇 차례 논의 과정을 거치면서 점차 상세한 부분들이 표현된 시퀀스 다이어그램입니다.
> * 상세함의 정도는 논의 내용에 따라 적절히 반영해주시기 바랍니다. 
>   - 참고 - 지나치게 상세하게 작성하는 것은 지양하고자 합니다 ^^;;

> [!NOTE] 
> (참고) 다음은 Butterfly의 Frontend와 Backend 간에 세부 호출 관계를 나타내고 있습니다.
> (참고) 이는 다음과 같이 포괄적으로 나타낼 수 있습니다.
> --> Web Console에서 Butterfly에 Workflow Tool view 요청
> --> Butterfly에서 Cicada의 Workflow template 조회 API 호출
> --> Butterfly에서 Cicada의 Task components API 호출
> --> Butterfly에서 Web Console로 Workflow Tool view 응답


```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant Butterfly as "Butterfly"
    participant Cicada as "Cicada"
    participant Damselfly as "Damselfly"

	%% [Steps for loading view]
	%% User accesses a view of Cloud Migration Workflow Management 
    User->>Browser: Access the Cloud Migration Workflow Management view
    activate Browser
    Browser->>Butterfly: Request the Cloud Migration Workflow Management view
    activate Butterfly    
	%% Retrieve the target models (cloud model) from Damselfly        
	Butterfly->>Damselfly: API call to GET /damselfly/cloudmodel
	activate Damselfly
	Damselfly-->>Butterfly: Return the cloud model list
	deactivate Damselfly
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display the Cloud Migration Workflow Management view
    deactivate Browser

	%% [Steps for processing user scenarios]
    %% Select a target model 
    User->>Browser: Select a target model
	activate Browser    
    %% Press "View Workflow Tool" and load the workflow (template)
    User->>Browser: press the hyperlink, View Worfklow Tool    
	
	%% Access the Workflow Tool base/background view
    Browser->>Butterfly: Request to load the Workflow Tool view
    activate Butterfly
    Butterfly-->Browser: Respond with the Workflow Tool base/background view
    Browser->>Browser: Display the Workflow Tool base/background view
    
    %% (On frontend, it's processed synchronously) Load a workflow (template) view
    Browser->Butterfly: Request to retrieve a workflow (template)
    Butterfly->>Cicada: API call to GET /cicada/workflow_template/{wftId}
	activate Cicada
	Cicada-->>Butterfly: Return the workflow (template)
	deactivate Cicada
    Butterfly-->>Browser: Respond with the workflow (template)
    Browser->>Browser: Display the workflow (template)

	%% (On frontend, it's processed synchronously) Load a list of task components view
    Browser->Butterfly: Request to retrieve a list of task components	
	Butterfly->>Cicada: API call to GET /cicada/task_component
	activate Cicada
	Cicada-->>Butterfly: Return the list of task components
	deactivate Cicada
	Butterfly-->>Browser: Respond with the list of task components
	Deactivate Butterfly
    Browser->>Browser: On the sidebar, display the list of task components 
    Deactivate Browser    

    %% Make/create a workflow
    opt (TBD) Modify the workflow (template)
	    User->>Browser: Drag and drop task components
	    activate Browser    
	    User->>Browser: Fill in the form
	end
    User->>Browser: Create the workflow
    Browser->>Butterfly: Request to crate the workflow
    activate Butterfly
    Butterfly->>Cicada: API call to POST /cicada/workflow
    activate Cicada
    Cicada-->>Butterfly: Return the created workflow content
    deactivate Cicada
    Butterfly-->>Browser: Respond with the workflow creation confirmation
    deactivate Butterfly
    Browser->>User: Display the confirmation    

```


## Run a workflow 

: Participants: Butterfly, Cicada

```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant Butterfly as "Butterfly"
    participant Cicada as "Cicada"
	
	%% Step 0: User accesses Cloud Migration Workflow Management View
    User->>Browser: Access the Cloud Migration Workflow Management view
    activate Browser
    Browser->>Butterfly: Request the Cloud Migration Workflow Management view
    activate Butterfly
    opt Retrieve the workflow list from Cicada
	    Butterfly->>Cicada: API call to GET /cicada/workflow
	    activate Cicada
	    Cicada-->>Butterfly: Return the workflow
	    deactivate Cicada
	end		
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Migration Model Management Interface
    deactivate Browser

    %% Select and run a workflow
    User->>Browser: Select a workflow
    activate Browser
    User->>Browser: Run the workflow
    Browser->>Butterfly: Request to run the workflow
    activate Butterfly
	Butterfly->>Cicada: API call to POST /cicada/workflow/{wfId}/run
	activate Cicada
	Cicada-->>Butterfly: Return pass or fail of the workflow-run
	deactivate Cicada
	Butterfly-->>Browser: Respond with the workflow-run confirmation
    deactivate Butterfly
    Browser-->>User: Display the confirmation
    deactivate Browser


	%% %% Get status of the workflow
    %% User->>Browser: Select a workflow
    %% activate Browser
    %% User->>Browser: Get status of the workflow
    %% Browser->>Butterfly: Request to get status of the workflow 
    %% activate Butterfly
	%% Butterfly->>Cicada: API call to GET /cicada/workflow/{wfId}/runs
	%% activate Cicada
	%% Cicada-->>Butterfly: Return task logs in the worfklow
	%% deactivate Cicada
	%% Butterfly-->>Browser: Respond with the task logs 
    %% deactivate Butterfly
    %% Browser-->>User: Display the task logs
    %% deactivate Browser
```


## Estimate cost for the recommend infra based on selected source model

: Participants: Butterfly, Beetle, Ant, Spider


```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Web Console"
    participant Butterfly as "Butterfly"
    participant Beetle as "Beetle"
    participant Ant as "Ant"
    participant Spider as "Spider"
	

    User->>Browser: Access to a view of the Recommended List (a list of servers)
    activate Browser
    Browser->>Butterfly: Request the Recommended Infra Model (a list of servers) with selected source model spec and image
    activate Butterfly
	
    %% Retrieve the recommendation models list from Beetle
	Butterfly->>Beetle: API call to POST /beetle/recommendation/mci
	activate Beetle
	Beetle-->>Butterfly: Return the recommendation infra model based on selected source model spec and image
	deactivate Beetle


    %% Retrieve estimate cost infromations from Ant
	loop For each recommendation infra model
		activate Ant
        Butterfly->>Ant: API call to POST /ant/api/v1/cost/estimate
        
        alt If Ant db does not have requested spec
            Ant->>Spider: API call to POST /spider/priceinfo/{ProductFamily}/{RegionName}
            Spider-->>Ant: Return price info response
        else If Ant db has requested spec
            Ant ->> Ant: Query price info for spec and save in DB
        end    
		Ant-->>Butterfly: Return cost estimate response
		deactivate Ant
	end

	Butterfly-->>Browser: Respond with the target model with estimate cost information
    deactivate Butterfly
    Browser-->>User: Display the recommended infra model with estimate cost informantion
    deactivate Browser

```


# Software infra migration (TBD)

