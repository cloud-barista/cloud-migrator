# Software migration scenarios

The sequence diagrams represent user scenarios for software migration.

> [!NOTE]
> Cloud-Migrator v0.4.0 통합 및 릴리스를 위한 Sequence Diagram 입니다.

> [!NOTE]
> Sequence Diagram은 대부분 2단계로 구성될 것 입니다. 다음 예시를 참고 바랍니다.
>
> 1. (사용자가 포털에서 페이지를 요청하는 단계)
>
> - Butterfly(서버)는 이를 위해 Subsystem들의 API를 호출하고
> - 얻은 정보를 바탕으로 View를 구성 및 제공하는 단계
>
> 2. (사용자가 해당 View 에서 마이그레이션을 요청하는 단계)
>
> - 마이그레이션에 필요한 정보를 입력 후 마이그레이션을 요청하고,
> - Butterfly(서버)가 관련 Subsystem 들의 API를 호출을 통해 마이그레이션을 수행 및 결과를 제공하는 단계

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

## Register the source computing environment and extract software information

: Participants: Butterfly, Honeybee, Source computing environment

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
    Honeybee->>SrcEnv: Install agent on the source servers
    activate SrcEnv
    SrcEnv-->>Honeybee: Return agent installation status
    deactivate SrcEnv
    Honeybee-->>Butterfly: Return the registered connection information
    deactivate Honeybee
    Butterfly-->>WebConsole: Respond with connection info and status (view/message)
    deactivate Butterfly
    WebConsole-->>User: Display connection registration and status
    deactivate WebConsole

    %% Step 3: Extract Software Information
    User->>WebConsole: Extract software information from source servers
    activate WebConsole
    WebConsole->>Butterfly: Send request to extract software information
    activate Butterfly
    Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgID}/import/software
    activate Honeybee
    Honeybee->>SrcEnv: Collect software packages, containers, and services
    activate SrcEnv
    SrcEnv-->>Honeybee: Return software information (packages, containers, etc.)
    deactivate SrcEnv
    Honeybee-->>Butterfly: Return the extracted software information
    deactivate Honeybee
    Butterfly-->>WebConsole: Respond with software extraction confirmation
    deactivate Butterfly
    WebConsole-->>User: Display software extraction status
    deactivate WebConsole
```

## Retrieve and refine software information from source environment, then save it

: Participants: Butterfly, Honeybee, Damselfly

- 해당 과정은 Honeybee로 부터 Source측의 정제된 Software 목록을 얻을 수 있는 Source의 Software Model 입니다.

```mermaid
sequenceDiagram
    participant User as User
    participant Browser as Web Console
    participant Butterfly as Butterfly
    participant Honeybee as Honeybee
    participant Damselfly as Damselfly

    %% Step 0: User accesses Software Migration Management View
    User->>Browser: Access the Software Migration Management view
    activate Browser
    Browser->>Butterfly: Request the Software Migration Management view
    activate Butterfly
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Software Migration Management Interface
    deactivate Browser

    %% Step 1: Get Raw Software Data
    User->>Browser: Retrieve raw software data from source
    activate Browser
    Browser->>Butterfly: Request to retrieve raw software data
    activate Butterfly
    Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgID}/software
    activate Honeybee
    Honeybee-->>Butterfly: Return raw software data
    deactivate Honeybee
    Butterfly-->>Browser: Respond with raw software data
    deactivate Butterfly
    Browser-->>User: Display raw software data in JSON editor
    deactivate Browser

    %% Step 2: Get Refined Software Data
    User->>Browser: Retrieve refined software data
    activate Browser
    Browser->>Butterfly: Request to retrieve refined software data
    activate Butterfly
    Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgID}/software/refined
    activate Honeybee
    Honeybee-->>Butterfly: Return refined software data (filtered and processed)
    deactivate Honeybee
    Butterfly-->>Browser: Respond with refined software data
    deactivate Butterfly
    Browser-->>User: Display refined software data in JSON editor
    deactivate Browser

    %% Optional Step: Save the source software model
    opt Save the source software model
        User->>Browser: Save source software model
        activate Browser
        Browser->>Butterfly: Request to save software model
        activate Butterfly
        Butterfly->>Damselfly: POST /damselfly/softwaremodel/source
        activate Damselfly
        Damselfly-->>Butterfly: Return the saved software model information
        deactivate Damselfly
        Butterfly-->>Browser: Respond with the save confirmation
        deactivate Butterfly
        Browser-->>User: Display the save confirmation
        deactivate Browser
    end
```

## Generate target software model, then save it

: Participants: Butterfly, Grasshopper, Honeybee, Damselfly

- 해당 과정은 Honeybee로 부터 Source측의 정제된 Software 목록을 얻고 난 후 사용자가 원하는 Software들을 선택하고 Migration 리스트를 요청하면 얻을 수 있는 Target의 Software Model 입니다.

- Migration List를 가져오는 Grasshopper API에 Honeybee로 부터 얻어온 Software 리스트를 전달하면 패키지 타입에 한해서 아래와 같은 패키지들을 제외하고 의존성으로 참조되는 패키지들을 제외 합니다.
- Honeybee로 부터 얻어온 Software 리스트에는 메인 패키지외에 의존성으로 같이 존재하거나 환경 구성을 위해 존재하는 패키지들이 있습니다. 해당 패키지들은 주요 메인 패키지를 설치할때 같이 설치되어 지기 때문에, Grasshopper에서는 해당 패키지들을 필터링 하고 마이그레이션 리스트를 작성합니다.
  - 라이브러리 패키지나 개발용 패키지(보통 lib으로 시작하거나, -dev로 끝나는 경우)
  - 문서 패키지 (.\*-doc, .\*-man)
  - 컨테이너 런타임 관련 패키지 (docker, podman, runc, ...)
  - Kernel 관련 패키지 (linux-generic.\*, kernel.\*, ...)
- 해당 동작은 Migration 과정을 단축시키고 동일한 패키지가 여러번 참조될 수 있는 중복 작업을 방지합니다.
- 이 과정으로 인해 Grasshopper를 통해서 Target 쪽으로 Migration 수행시 실제로 참조되는 Software 목록을 확인 할 수 있습니다.
- 이 목록은 Target에 Software들을 구성하기 위한 Target Software Model이 됩니다.

1. 사용자는 Web Console을 통해 저장된 Source Software 목록을 조회합니다.
2. Butterlfy 모듈이 Damselfly로 부터 저장된 Source Software 목록을 가져옵니다.
3. Web Console에 저장된 Source Software 목록이 표출됩니다.
4. 사용자가 Web Console을 통해 Target Software 목록 요청을 합니다.
5. Butterfly 모듈이 Grasshopper 모듈로 Migration List를 가져오는 API에 Source Software 목록을 전달합니다.
6. 패키지 타입에 한해서 의존성으로 같이 존재하거나 환경 구성을 위해 존재하는 패키지들을 제외 합니다.
7. Web Console에 Software Migration List를 표출하게 됩니다. 이는 Target Software Model이 됩니다.
8. 사용자가 Web Console을 통해 Target Software Model 저장 요청을 합니다.
9. Butterlfy 모듈이 Damselfly 모듈로 Target Software Model을 저장하는 API를 요청합니다.
10. 저장된 Target Software는 추후 Target에 Software Migration을 수행하는데 사용됩니다.

```mermaid
sequenceDiagram
    participant User as User
    participant Browser as Web Console
    participant Butterfly as Butterfly
    participant Grasshopper as Grasshopper
    participant Honeybee as Honeybee
    participant Damselfly as Damselfly

    %% Step 0: User accesses Software Migration Management View
    User->>Browser: Access the Software Migration Management view
    activate Browser
    Browser->>Butterfly: Request the Software Migration Management view
    activate Butterfly
    
    %% Step 1: Retrieve saved Source Software Model list from Damselfly
    Butterfly->>Damselfly: API call to GET /damselfly/softwaremodel/source (list all source software models)
    activate Damselfly
    Damselfly-->>Butterfly: Return list of saved source software models
    deactivate Damselfly
    
    Butterfly-->>Browser: Respond with the view and source software model list
    deactivate Butterfly
    Browser-->>User: Display Software Migration Management Interface with source software model list
    deactivate Browser

    %% Step 2: Load specific Source Software Model
    User->>Browser: Select and load specific source software model
    activate Browser
    Browser->>Butterfly: Request to load specific source software model
    activate Butterfly
    Butterfly->>Damselfly: API call to GET /damselfly/softwaremodel/source/{id} (retrieve specific source software model)
    activate Damselfly
    Damselfly-->>Butterfly: Return specific source software model data
    deactivate Damselfly
    Butterfly-->>Browser: Respond with source software model data
    deactivate Butterfly
    Browser-->>User: Display selected source software model details
    deactivate Browser

    %% Step 3: Generate Target Software Migration List
    User->>Browser: Request to generate target software migration list
    activate Browser
    Browser->>Butterfly: Request to generate target software migration list
    activate Butterfly
    Butterfly->>Grasshopper: API call to POST /grasshopper/software/migration_list (with source software model data)
    activate Grasshopper
    Note over Grasshopper: Filter out dependency packages:<br/>- Library packages (lib.*, *-dev)<br/>- Documentation packages (*-doc, *-man)<br/>- Container runtimes (docker, podman, runc, ...)<br/>- Kernel packages (linux-generic.*, kernel.*, ...)<br/>- Environment setup packages
    Grasshopper-->>Butterfly: Return filtered migration list (Target Software Model)
    deactivate Grasshopper
    Butterfly-->>Browser: Respond with target software migration list
    deactivate Butterfly
    Browser-->>User: Display target software migration list (Target Software Model)
    deactivate Browser

    %% Step 4: Save Target Software Model
    User->>Browser: Request to save Target Software Model
    activate Browser
    Browser->>Butterfly: Request to save Target Software Model
    activate Butterfly
    Butterfly->>Damselfly: API call to POST /damselfly/softwaremodel/target (save target software model)
    activate Damselfly
    Damselfly-->>Butterfly: Return saved Target Software Model confirmation
    deactivate Damselfly
    Butterfly-->>Browser: Respond with save confirmation
    deactivate Butterfly
    Browser-->>User: Display Target Software Model save confirmation
    deactivate Browser
    
    Note over User, Damselfly: The saved Target Software Model will be used<br/>for software migration execution
```

## Execute software migration

: Participants: Butterfly, Cicada, Damselfly, Grasshopper, Honeybee, Tumblebug

```mermaid
sequenceDiagram
    participant User as User
    participant Browser as Web Console
    participant Butterfly as Butterfly
    participant Damselfly as Damselfly
    participant Cicada as Cicada
    participant Grasshopper as Grasshopper
    participant Honeybee as Honeybee
    participant Tumblebug as Tumblebug
    participant SourceVM as Source VM
    participant TargetVM as Target VM

    %% Step 0: User accesses Target Software Model Management View
    User->>Browser: Access the Target Software Model Management view
    activate Browser
    Browser->>Butterfly: Request the Target Software Model Management view
    activate Butterfly
    
    %% Retrieve saved Target Software Models
    Butterfly->>Damselfly: API call to GET /damselfly/softwaremodel/target (list target software models)
    activate Damselfly
    Damselfly-->>Butterfly: Return list of saved target software models
    deactivate Damselfly
    
    Butterfly-->>Browser: Respond with the view and target software models
    deactivate Butterfly
    Browser-->>User: Display Target Software Model Management Interface
    deactivate Browser

    %% Step 1: Select Target Software Model
    User->>Browser: Select target software model for migration
    activate Browser
    Browser->>Butterfly: Send request with selected target software model ID
    activate Butterfly
    
    %% Retrieve specific Target Software Model
    Butterfly->>Damselfly: API call to GET /damselfly/softwaremodel/target/{id} (retrieve specific target software model)
    activate Damselfly
    Damselfly-->>Butterfly: Return target software model data
    deactivate Damselfly
    
    Butterfly-->>Browser: Respond with target software model data
    deactivate Butterfly
    Browser-->>User: Display selected target software model details
    deactivate Browser

    %% Step 2: Access VM Management and Query Available VMs
    User->>Browser: Access VM Management menu in portal
    activate Browser
    Browser->>Butterfly: Request VM Management view
    activate Butterfly
    
    %% Query available VMs from Tumblebug
    Butterfly->>Tumblebug: API call to GET /tumblebug/ns/{nsId}/mcis (query available VMs)
    activate Tumblebug
    Tumblebug-->>Butterfly: Return list of available VMs and their details
    deactivate Tumblebug
    
    Butterfly-->>Browser: Respond with VM list and details
    deactivate Butterfly
    Browser-->>User: Display available VMs for migration target
    deactivate Browser

    %% Step 3: Query Software Migration Workflow Templates
    User->>Browser: Access Workflow Template selection
    activate Browser
    Browser->>Butterfly: Request software migration workflow templates
    activate Butterfly
    
    %% Query Software Migration Workflow Templates from Cicada
    Butterfly->>Cicada: API call to GET /cicada/workflow/templates (query software migration workflow templates)
    activate Cicada
    Note over Cicada: Return available templates:<br/>- migrate_software_workflow<br/>- custom migration templates<br/>- task component configurations
    Cicada-->>Butterfly: Return available software migration workflow templates
    deactivate Cicada
    
    Butterfly-->>Browser: Respond with workflow templates
    deactivate Butterfly
    Browser-->>User: Display available workflow templates
    deactivate Browser

    %% Step 4: Create Migration Workflow
    User->>Browser: Select target VM and workflow template, create migration workflow
    activate Browser
    Browser->>Butterfly: Send workflow creation request with target VM and template
    activate Butterfly
    Butterfly->>Cicada: API call to POST /cicada/workflow (create migration workflow based on target software model, target VM, and selected template)
    activate Cicada
    Note over Cicada: Create software migration workflow:<br/>- Use target software model data<br/>- Apply selected workflow template<br/>- Configure target VM information<br/>- Set task dependencies and execution order<br/>- Generate workflow DAG
    Cicada-->>Butterfly: Return created workflow ID and configuration
    deactivate Cicada
    Butterfly-->>Browser: Respond with workflow creation confirmation
    deactivate Butterfly
    Browser-->>User: Display created workflow details and workflow ID
    deactivate Browser

    %% Step 5: Execute Migration Workflow
    User->>Browser: Execute the created migration workflow
    activate Browser
    Browser->>Butterfly: Send workflow execution request with workflow ID
    activate Butterfly
    Butterfly->>Cicada: API call to POST /cicada/workflow/{workflowId}/run (execute the migration workflow)
    activate Cicada
    Note over Cicada: Execute workflow:<br/>- Start workflow execution<br/>- Trigger task components in sequence<br/>- Monitor task dependencies
    
    %% Cicada triggers Grasshopper tasks through workflow execution
    Cicada->>Grasshopper: Execute software migration tasks (via workflow)
    activate Grasshopper
    
    %% Establish SSH connections
    Grasshopper->>Honeybee: Get source connection info
    activate Honeybee
    Note over Honeybee: Decrypt connection credentials<br/>using RSA private key
    Honeybee-->>Grasshopper: Return decrypted connection info
    deactivate Honeybee
    
    Grasshopper->>Tumblebug: Get target VM connection info
    activate Tumblebug
    Tumblebug-->>Grasshopper: Return target VM access info
    deactivate Tumblebug
    
    Grasshopper->>SourceVM: Establish SSH connection
    activate SourceVM
    Grasshopper->>TargetVM: Establish SSH connection  
    activate TargetVM
    
    %% Execute migration for each software package in Target Software Model
    loop For each software package in Target Software Model
        Note over Grasshopper: Update status to "installing"<br/>Process software from target software model
        Grasshopper->>TargetVM: Run Ansible playbook for package installation
        TargetVM-->>Grasshopper: Return installation result
        
        Grasshopper->>SourceVM: Copy configuration files and data
        SourceVM-->>Grasshopper: Return copied files
        Grasshopper->>TargetVM: Transfer configuration files
        
        Grasshopper->>SourceVM: Extract service configuration
        SourceVM-->>Grasshopper: Return service settings
        Grasshopper->>TargetVM: Configure and start services
        TargetVM-->>Grasshopper: Return service status
        
        Note over Grasshopper: Update status to "finished" or "failed"
    end
    
    deactivate SourceVM
    deactivate TargetVM
    
    Grasshopper-->>Cicada: Return migration task results and status
    deactivate Grasshopper
    Note over Cicada: Monitor workflow execution:<br/>- Track task completion<br/>- Update workflow status<br/>- Generate execution report<br/>- Handle task failures and retries
    Cicada-->>Butterfly: Return workflow execution status and results
    deactivate Cicada
    Butterfly-->>Browser: Respond with workflow execution status
    deactivate Butterfly
    Browser-->>User: Display workflow execution status and progress
    deactivate Browser
```

## Monitor software migration progress and logs

: Participants: Butterfly, Grasshopper

```mermaid
sequenceDiagram
    participant User as User
    participant Browser as Web Console
    participant Butterfly as Butterfly
    participant Grasshopper as Grasshopper

    %% Step 0: User accesses Migration Monitoring View
    User->>Browser: Access the Migration Monitoring view
    activate Browser
    Browser->>Butterfly: Request the Migration Monitoring view
    activate Butterfly
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Migration Monitoring Interface
    deactivate Browser

    %% Step 1: Monitor Migration Progress
    User->>Browser: Monitor migration progress
    activate Browser
    loop Monitor progress periodically
        Browser->>Butterfly: Request migration status
        activate Butterfly
        Butterfly->>Grasshopper: API call to GET /grasshopper/software/migrate/status/{executionId}
        activate Grasshopper
        Note over Grasshopper: Check execution status:<br/>- ready, installing, finished, failed<br/>- Progress percentage<br/>- Current package being processed
        Grasshopper-->>Butterfly: Return real-time migration status
        deactivate Grasshopper
        Butterfly-->>Browser: Respond with migration progress
        deactivate Butterfly
        Browser-->>User: Update migration progress display
    end
    deactivate Browser

    %% Step 2: View Migration Logs
    User->>Browser: View detailed migration logs
    activate Browser
    Browser->>Butterfly: Request migration logs
    activate Butterfly
    Butterfly->>Grasshopper: API call to GET /grasshopper/software/migrate/log/{executionId}
    activate Grasshopper
    Note over Grasshopper: Retrieve logs:<br/>- install.log (installation details)<br/>- migration.log (configuration and service logs)<br/>- Error messages and stack traces
    Grasshopper-->>Butterfly: Return migration logs
    deactivate Grasshopper
    Butterfly-->>Browser: Respond with detailed logs
    deactivate Butterfly
    Browser-->>User: Display installation and migration logs
    deactivate Browser
```
