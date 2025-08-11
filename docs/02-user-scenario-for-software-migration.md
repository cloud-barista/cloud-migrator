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

## Retrieve and refine software information from source environment

: Participants: Butterfly, Honeybee, Damselfly

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
        Butterfly->>Damselfly: POST /damselfly/softwaremodel
        activate Damselfly
        Damselfly-->>Butterfly: Return the saved software model information
        deactivate Damselfly
        Butterfly-->>Browser: Respond with the save confirmation
        deactivate Butterfly
        Browser-->>User: Display the save confirmation
        deactivate Browser
    end
```

## Generate software migration list and plan

: Participants: Butterfly, Grasshopper, Honeybee

- 해당 과정은 Honeybee로 부터 Source측의 정제된 Software 목록을 얻고 난 후 사용자의 선택으로 Migration 대상 리스트가 선택되어진 상태에서 Grasshopper에 전달한 후의 동작입니다.
- Migration List를 가져오는 Grasshopper API에 Honeybee로 부터 얻어온 Software 리스트를 전달하면 패키지 타입에 한해서 아래와 같은 패키지들을 제외하고 의존성으로 참조되는 패키지들을 제외 합니다.
- Honeybee로 부터 얻어온 Software 리스트에는 메인 패키지외에 의존성으로 같이 존재하거나 환경 구성을 위해 존재하는 패키지들이 있습니다. 해당 패키지들은 주요 메인 패키지를 설치할때 같이 설치되어 지기 때문에, Grasshopper에서는 해당 패키지들을 필터링 하고 마이그레이션 리스트를 작성합니다.
  - 라이브러리 패키지나 개발용 패키지(보통 lib으로 시작하거나, -dev로 끝나는 경우)
  - 문서 패키지 (.\*-doc, .\*-man)
  - 컨테이너 런타임 관련 패키지 (docker, podman, runc, ...)
  - Kernel 관련 패키지 (linux-generic.\*, kernel.\*, ...)
- 해당 동작은 Migration 과정을 단축시키고 동일한 패키지가 여러번 참조될 수 있는 중복 작업을 방지합니다.
- 이 과정으로 인해 Honeybee에서 얻어온 Software 목록에서는 전체 소프트웨어 목록을 확인 할 수 있고, Migration List API를 통해 가져온 Software 목록에서는 실제로 Migration 수행시 참조되는 Software 목록을 확인 할 수 있습니다.

1. 사용자는 Honeybee로 부터 Software 목록을 얻은 후에 Migration을 수행할 Software들을 선택하게 됩니다.
2. Migration List를 가져오는 Grasshopper API에 선택한 Software 리스트를 전달합니다. 이때 Migration 대상 Source 정보를 같이 전달합니다.
3. Honeybee를 통해서 전달받은 Source 정보가 존재하는지 검증합니다.
4. 패키지 타입에 한해서 의존성으로 같이 존재하거나 환경 구성을 위해 존재하는 패키지들을 제외 합니다.
5. Portal에 Software Migration List를 표출하게 됩니다. 이는 Software Migration List이자, Plan에 해당됩니다.

```mermaid
sequenceDiagram
    participant User as User
    participant Browser as Web Console
    participant Butterfly as Butterfly
    participant Grasshopper as Grasshopper
    participant Honeybee as Honeybee

    %% Step 0: User accesses Software Migration Management View
    User->>Browser: Access the Software Migration Management view
    activate Browser
    Browser->>Butterfly: Request the Software Migration Management view
    activate Butterfly
    opt Retrieve refined software data from Honeybee
        Butterfly->>Honeybee: API call to GET /honeybee/source_group/{sgID}/software/refined
        activate Honeybee
        Honeybee-->>Butterfly: Return refined software data
        deactivate Honeybee
    end
    Butterfly-->>Browser: Respond with the view
    deactivate Butterfly
    Browser-->>User: Display Software Migration Management Interface
    deactivate Browser

    %% Step 1: Generate Migration List
    User->>Browser: Generate software migration list
    activate Browser
    Browser->>Butterfly: Request to generate migration list
    activate Butterfly
    Butterfly->>Grasshopper: API call to POST /grasshopper/software/migration_list
    activate Grasshopper
    Note over Grasshopper: Filter out system packages:<br/>- Library packages (lib.*-dev, *-doc, *-man, ...)<br/>- Container runtimes (.*docker.*, .*podman.*, ...)<br/>- Kernel packages (linux-generic.*, kernel.*, ...)<br/>- Package managers (apt, yum, dnf, ...)
    Grasshopper->>Honeybee: Validate connection info
    activate Honeybee
    Honeybee-->>Grasshopper: Return connection validation result
    deactivate Honeybee
    Grasshopper-->>Butterfly: Return filtered migration package list
    deactivate Grasshopper
    Butterfly-->>Browser: Respond with migration list
    deactivate Butterfly
    Browser-->>User: Display migration plan with filtered packages
    deactivate Browser

    %% Step 2: Review and Modify Migration List
    opt Review and modify migration list
        User->>Browser: Review and modify migration packages
        activate Browser
        Note over Browser: User can:<br/>- Remove unwanted packages<br/>- Modify package versions<br/>- Set custom configurations<br/>- Define data paths
        Browser-->>User: Display modified migration plan
        deactivate Browser
    end
```

## Execute software migration

: Participants: Butterfly, Cicada, Grasshopper, Honeybee, Tumblebug

```mermaid
sequenceDiagram
    participant User as User
    participant Browser as Web Console
    participant Butterfly as Butterfly
    participant Cicada as Cicada
    participant Grasshopper as Grasshopper
    participant Honeybee as Honeybee
    participant Tumblebug as Tumblebug
    participant SourceVM as Source VM
    participant TargetVM as Target VM

    %% Step 0: User accesses Software Migration Execution View
    User->>Browser: Access the Software Migration Execution view
    activate Browser
    Browser->>Butterfly: Request the Software Migration Execution view
    activate Butterfly
    Butterfly-->>Browser: Respond with the view and available target VMs
    deactivate Butterfly
    Browser-->>User: Display Migration Execution Interface
    deactivate Browser

    %% Step 1: Create Migration Workflow
    User->>Browser: Select target VM and execute software migration
    activate Browser
    Browser->>Butterfly: Send migration execution request
    activate Butterfly
    Butterfly->>Cicada: API call to POST /cicada/workflow to create migration workflow
    activate Cicada
    Note over Cicada: Create software migration workflow:<br/>- Generate workflow template<br/>- Configure migration tasks<br/>- Set task dependencies
    Cicada->>Grasshopper: API call to POST /grasshopper/software/migrate
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
    
    %% Execute migration for each software package
    loop For each software package in migration list
        Note over Grasshopper: Update status to "installing"
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
    
    Grasshopper-->>Cicada: Return migration execution ID and status
    deactivate Grasshopper
    Note over Cicada: Monitor workflow execution:<br/>- Track task completion<br/>- Update workflow status<br/>- Generate execution report
    Cicada-->>Butterfly: Return workflow execution ID and status
    deactivate Cicada
    Butterfly-->>Browser: Respond with migration execution confirmation
    deactivate Butterfly
    Browser-->>User: Display migration execution status and execution ID
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
