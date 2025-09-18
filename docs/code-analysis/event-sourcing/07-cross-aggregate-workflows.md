# Cross-Aggregate Workflow Documentation

## Overview

This document outlines the complex cross-aggregate workflows in OpenELIS-Global-2 that span multiple domain aggregates. These workflows represent the core laboratory business processes and demonstrate how domain events flow between aggregates to orchestrate complete laboratory operations.

## Primary Laboratory Workflows

### 1. Standard Laboratory Testing Workflow

```mermaid
flowchart TD
    A[Electronic Order Received] --> B{Order Valid?}
    B -->|No| C[Order Rejected]
    B -->|Yes| D[Patient Verification]
    D --> E{Patient Exists?}
    E -->|No| F[Patient Registration]
    E -->|Yes| G[Sample Creation]
    F --> G
    G --> H[Sample Collection]
    H --> I{Sample Acceptable?}
    I -->|No| J[Sample Rejection - QA Event]
    I -->|Yes| K[Analysis Creation]
    K --> L[Result Entry]
    L --> M{Results Valid?}
    M -->|No| N[Technical Rejection - QA Event]
    M -->|Yes| O[Biologist Review]
    O --> P{Approved?}
    P -->|No| Q[Biologist Rejection - QA Event]
    P -->|Yes| R[Results Finalized]
    R --> S[Report Generation]
    S --> T[Result Transmission]
    
    C --> U[End]
    J --> V[QA Investigation]
    N --> V
    Q --> V
    V --> W[Corrective Action]
    W --> X{Retry?}
    X -->|Yes| K
    X -->|No| U
    T --> U
```

### 2. Referral Testing Workflow

```mermaid
flowchart TD
    A[Analysis Created] --> B{Can Process Locally?}
    B -->|Yes| C[Standard Testing Workflow]
    B -->|No| D[Referral Required]
    D --> E[External Lab Selection]
    E --> F[Referral Documentation]
    F --> G[FHIR Task Creation]
    G --> H[Referral Transmission]
    H --> I{Transmission Success?}
    I -->|No| J[Communication Error - QA Event]
    I -->|Yes| K[External Processing]
    K --> L[Status Monitoring]
    L --> M{Results Received?}
    M -->|No| N{SLA Exceeded?}
    N -->|Yes| O[Escalation - QA Event]
    N -->|No| L
    M -->|Yes| P[Result Validation]
    P --> Q{Results Valid?}
    Q -->|No| R[Result Rejection - QA Event]
    Q -->|Yes| S[Local Integration]
    S --> T[Analysis Finalized]
    T --> U[Report Generation]
    
    C --> U
    J --> V[QA Investigation]
    O --> V
    R --> V
    V --> W[Corrective Action]
    W --> X{Retry?}
    X -->|Yes| D
    X -->|No| Y[Analysis Canceled]
    U --> Z[End]
    Y --> Z
```

### 3. Quality Assurance Workflow

```mermaid
flowchart TD
    A[Quality Issue Detected] --> B[QA Event Creation]
    B --> C{Issue Severity}
    C -->|Critical| D[Immediate Containment]
    C -->|Major| E[Investigation Assignment]
    C -->|Minor| F[Standard Investigation]
    
    D --> G[Management Notification]
    G --> H[Emergency Investigation]
    
    E --> I[Investigator Assignment]
    F --> I
    H --> I
    
    I --> J[Root Cause Analysis]
    J --> K{Root Cause Found?}
    K -->|No| L[Extended Investigation]
    K -->|Yes| M[CAPA Planning]
    
    L --> J
    
    M --> N[Action Plan Approval]
    N --> O[CAPA Implementation]
    O --> P[Implementation Verification]
    P --> Q{Verification Passed?}
    Q -->|No| R[Implementation Revision]
    Q -->|Yes| S[Effectiveness Review]
    
    R --> O
    
    S --> T{Effective?}
    T -->|No| U[Additional Actions Required]
    T -->|Yes| V[QA Event Closure]
    
    U --> M
    V --> W[Lessons Learned Documentation]
    W --> X[Process Improvement]
    X --> Y[End]
```

## Cross-Aggregate Event Flows

### 1. Order to Results Flow

```mermaid
sequenceDiagram
    participant Order as Order Aggregate
    participant Patient as Patient Aggregate
    participant Sample as Sample Aggregate
    participant Analysis as Analysis Aggregate
    participant QA as QA Aggregate
    participant Referral as Referral Aggregate
    
    Order->>Order: OrderReceived
    Order->>Patient: PatientVerificationRequired
    Patient->>Patient: PatientValidated
    Patient->>Order: PatientReady
    Order->>Sample: SampleCreationRequested
    Sample->>Sample: SampleRegistered
    Sample->>Analysis: AnalysisCreationRequested
    Analysis->>Analysis: AnalysisCreated
    
    alt Local Testing
        Analysis->>Analysis: ResultsEntered
        Analysis->>Analysis: AnalysisFinalized
    else Referral Required
        Analysis->>Referral: ReferralCreated
        Referral->>Referral: ReferralSent
        Referral->>Referral: ReferralResultReceived
        Referral->>Analysis: ExternalResultsIntegrated
        Analysis->>Analysis: AnalysisFinalized
    end
    
    Analysis->>Sample: AllAnalysesComplete
    Sample->>Sample: SampleCompleted
    Sample->>Order: OrderFulfilled
    Order->>Order: OrderRealized
```

### 2. Quality Exception Flow

```mermaid
sequenceDiagram
    participant Trigger as Triggering Aggregate
    participant QA as QA Aggregate
    participant Management as Management
    participant Investigation as Investigation Team
    participant Source as Source Aggregate
    
    Trigger->>QA: QualityIssueDetected
    QA->>QA: QaEventReported
    QA->>Management: IssueEscalated
    Management->>Investigation: InvestigationAssigned
    Investigation->>QA: InvestigationStarted
    QA->>QA: RootCauseAnalysisBegun
    Investigation->>QA: RootCauseIdentified
    QA->>QA: CAPAPlanned
    QA->>Source: CorrectiveActionRequired
    Source->>QA: ActionImplemented
    QA->>QA: VerificationCompleted
    QA->>QA: QaEventClosed
    QA->>Management: ResolutionConfirmed
```

### 3. Patient Demographics Synchronization

```mermaid
sequenceDiagram
    participant Patient as Patient Aggregate
    participant Sample as Sample Aggregate
    participant Analysis as Analysis Aggregate
    participant Report as Reporting System
    participant External as External Systems
    
    Patient->>Patient: PatientDemographicsUpdated
    Patient->>Sample: UpdateSampleReferences
    Sample->>Sample: PatientInfoSynchronized
    Sample->>Analysis: UpdateAnalysisMetadata
    Analysis->>Analysis: PatientInfoUpdated
    Analysis->>Report: RefreshReportData
    Report->>External: SynchronizeExternalSystems
```

## Saga Patterns for Cross-Aggregate Transactions

### 1. Sample Processing Saga

```mermaid
stateDiagram-v2
    [*] --> SampleRegistration : Start Saga
    SampleRegistration --> AnalysisCreation : Sample Created
    AnalysisCreation --> ResultEntry : Analysis Ready
    ResultEntry --> Validation : Results Entered
    Validation --> Finalization : Validation Passed
    Finalization --> Completion : Results Finalized
    Completion --> [*] : Saga Complete
    
    SampleRegistration --> CompensateSample : Registration Failed
    AnalysisCreation --> CompensateAnalysis : Creation Failed
    ResultEntry --> CompensateResults : Entry Failed
    Validation --> CompensateValidation : Validation Failed
    Finalization --> CompensateFinalization : Finalization Failed
    
    CompensateSample --> [*] : Saga Aborted
    CompensateAnalysis --> CompensateSample
    CompensateResults --> CompensateAnalysis
    CompensateValidation --> CompensateResults
    CompensateFinalization --> CompensateValidation
```

### 2. Referral Processing Saga

```mermaid
stateDiagram-v2
    [*] --> ReferralCreation : Start Saga
    ReferralCreation --> Documentation : Referral Created
    Documentation --> Transmission : Documentation Complete
    Transmission --> ExternalProcessing : Sent Successfully
    ExternalProcessing --> ResultReception : Processing Complete
    ResultReception --> LocalIntegration : Results Received
    LocalIntegration --> AnalysisCompletion : Integration Complete
    AnalysisCompletion --> [*] : Saga Complete
    
    ReferralCreation --> CompensateReferral : Creation Failed
    Documentation --> CompensateDocumentation : Documentation Failed
    Transmission --> CompensateTransmission : Transmission Failed
    ExternalProcessing --> CompensateExternal : External Processing Failed
    ResultReception --> CompensateReception : Reception Failed
    LocalIntegration --> CompensateIntegration : Integration Failed
    
    CompensateReferral --> [*] : Saga Aborted
    CompensateDocumentation --> CompensateReferral
    CompensateTransmission --> CompensateDocumentation
    CompensateExternal --> CompensateTransmission
    CompensateReception --> CompensateExternal
    CompensateIntegration --> CompensateReception
```

## Event Choreography Patterns

### 1. Event-Driven Sample Lifecycle

```mermaid
graph LR
    A[OrderReceived] --> B[SampleRegistered]
    B --> C[AnalysisCreated]
    C --> D[ResultsEntered]
    D --> E[TechnicalValidation]
    E --> F[BiologistApproval]
    F --> G[AnalysisFinalized]
    G --> H[SampleCompleted]
    H --> I[ReportGenerated]
    
    D --> J[TechnicalRejection]
    F --> K[BiologistRejection]
    J --> L[QaEventCreated]
    K --> L
    L --> M[QaInvestigationStarted]
    M --> N[CorrectiveActionPlanned]
    N --> O[CAPAImplemented]
    O --> P[QaEventClosed]
    P --> Q[RetestRequired]
    Q --> C
```

### 2. Priority-Based Processing

```mermaid
graph TD
    A[StatOrderReceived] --> B[UrgentSampleRegistered]
    B --> C[PriorityAnalysisCreated]
    C --> D[ExpeditedProcessing]
    D --> E[RushResultValidation]
    E --> F[ImmediateReporting]
    
    G[RoutineOrderReceived] --> H[StandardSampleRegistered]
    H --> I[RoutineAnalysisCreated]
    I --> J[StandardProcessing]
    J --> K[NormalValidation]
    K --> L[StandardReporting]
    
    M[ASAPOrderReceived] --> N[ExpeditedSampleRegistered]
    N --> O[ASAPAnalysisCreated]
    O --> P[AcceleratedProcessing]
    P --> Q[FastTrackValidation]
    Q --> R[PriorityReporting]
```

## Consistency Patterns

### 1. Eventual Consistency with Compensation

```java
// Example: Sample completion with analysis dependencies
@EventHandler
public void on(AnalysisFinalized event) {
    // Check if all analyses for sample are complete
    Sample sample = sampleRepository.get(event.getSampleId());
    List<Analysis> analyses = analysisRepository.getBySampleId(event.getSampleId());
    
    boolean allComplete = analyses.stream()
        .allMatch(analysis -> analysis.getStatus() == AnalysisStatus.FINALIZED);
    
    if (allComplete) {
        // Eventual consistency - sample completion
        eventBus.publish(new SampleCompleted(sample.getId()));
    }
}

@EventHandler
public void on(AnalysisRejected event) {
    // Compensation - sample cannot be completed
    Sample sample = sampleRepository.get(event.getSampleId());
    if (sample.getStatus() == SampleStatus.COMPLETED) {
        // Compensate - revert sample status
        eventBus.publish(new SampleStatusReverted(sample.getId()));
    }
}
```

### 2. Distributed Locks for Critical Sections

```java
// Example: Patient merge operation across aggregates
@Transactional
public void mergePatients(String primaryPatientId, String duplicatePatientId) {
    // Acquire distributed locks
    DistributedLock patientLock = lockService.acquire("patient:" + primaryPatientId);
    DistributedLock sampleLock = lockService.acquire("samples:" + duplicatePatientId);
    
    try {
        // Atomic cross-aggregate operation
        Patient primary = patientRepository.get(primaryPatientId);
        Patient duplicate = patientRepository.get(duplicatePatientId);
        
        // Merge patient data
        primary.merge(duplicate);
        patientRepository.save(primary);
        
        // Update sample references
        List<Sample> samples = sampleRepository.getByPatientId(duplicatePatientId);
        samples.forEach(sample -> {
            sample.setPatientId(primaryPatientId);
            sampleRepository.save(sample);
        });
        
        // Soft delete duplicate
        duplicate.markAsDeleted();
        patientRepository.save(duplicate);
        
        // Publish merge event
        eventBus.publish(new PatientMerged(primaryPatientId, duplicatePatientId));
        
    } finally {
        patientLock.release();
        sampleLock.release();
    }
}
```

## Dapr Workflow Integration Points

### 1. Complex Sample Processing Workflow

```yaml
# Dapr workflow definition for sample processing
name: SampleProcessingWorkflow
version: 1.0
spec:
  activities:
    - name: ValidateSample
      type: activity
      input: sampleData
      timeout: 5m
      
    - name: CreateAnalyses
      type: activity
      input: sampleId
      timeout: 10m
      
    - name: ProcessResults
      type: parallel
      branches:
        - name: TechnicalValidation
          activities:
            - ValidateResults
            - CheckQC
        - name: BiologistReview
          activities:
            - ReviewResults
            - ApproveResults
            
    - name: FinalizeResults
      type: activity
      input: analysisResults
      timeout: 5m
      
  error_handling:
    - activity: ValidateSample
      retry:
        max_attempts: 3
        backoff: exponential
      compensation: RejectSample
      
    - activity: ProcessResults
      timeout: 2h
      compensation: CancelProcessing
```

### 2. QA Investigation Workflow

```yaml
# Dapr workflow for QA investigation process
name: QAInvestigationWorkflow
version: 1.0
spec:
  activities:
    - name: AssignInvestigator
      type: human_task
      assignee: qa_team
      timeout: 1h
      
    - name: ConductRCA
      type: activity
      input: qaEventId
      timeout: 48h
      
    - name: PlanCAPA
      type: human_task
      assignee: qa_manager
      timeout: 24h
      
    - name: ImplementActions
      type: parallel
      branches:
        - name: ImmediateActions
          timeout: 4h
        - name: LongTermActions
          timeout: 30d
          
    - name: VerifyEffectiveness
      type: activity
      input: capaResults
      timeout: 7d
      
  escalation:
    - condition: timeout
      escalate_to: qa_director
      notification: immediate
```

## Performance Considerations

### 1. Event Ordering Guarantees

```java
// Ensure proper event ordering for sample lifecycle
@Component
public class SampleEventOrdering {
    
    @EventHandler
    @Order(1)
    public void handleSampleRegistered(SampleRegistered event) {
        // Process first
    }
    
    @EventHandler
    @Order(2)
    public void handleAnalysisCreated(AnalysisCreated event) {
        // Process after sample registration
    }
    
    @EventHandler
    @Order(3)
    public void handleSampleCompleted(SampleCompleted event) {
        // Process last
    }
}
```

### 2. Batch Processing for High Volume

```java
// Batch processing for high-volume order processing
@Service
public class BatchOrderProcessor {
    
    @Scheduled(fixedDelay = 30000) // Every 30 seconds
    public void processBatchOrders() {
        List<ElectronicOrder> pendingOrders = orderRepository
            .findByStatus(OrderStatus.PENDING)
            .limit(100); // Process in batches of 100
            
        for (ElectronicOrder order : pendingOrders) {
            processOrderAsync(order);
        }
    }
    
    @Async
    public CompletableFuture<Void> processOrderAsync(ElectronicOrder order) {
        // Asynchronous order processing
        return CompletableFuture.runAsync(() -> {
            orderProcessingService.process(order);
        });
    }
}
```

## Monitoring and Observability

### 1. Cross-Aggregate Tracing

```java
// Distributed tracing for cross-aggregate workflows
@Component
public class WorkflowTracing {
    
    @EventHandler
    public void handleSampleRegistered(SampleRegistered event) {
        try (Scope scope = tracer.spanBuilder("sample-processing")
                .setParent(Context.current())
                .startScopedSpan()) {
            
            Span.current().setAttribute("sample.id", event.getSampleId());
            Span.current().setAttribute("workflow.stage", "registration");
            
            // Process event
            sampleProcessingService.process(event);
        }
    }
}
```

### 2. Workflow Health Monitoring

```java
// Health checks for cross-aggregate workflows
@Component
public class WorkflowHealthIndicator implements HealthIndicator {
    
    @Override
    public Health health() {
        // Check critical workflow components
        boolean samplesProcessing = checkSampleProcessingHealth();
        boolean qaWorkflowHealthy = checkQAWorkflowHealth();
        boolean referralsWorking = checkReferralWorkflowHealth();
        
        if (samplesProcessing && qaWorkflowHealthy && referralsWorking) {
            return Health.up()
                .withDetail("workflows", "All workflows operational")
                .build();
        } else {
            return Health.down()
                .withDetail("sample_processing", samplesProcessing)
                .withDetail("qa_workflow", qaWorkflowHealthy)
                .withDetail("referral_workflow", referralsWorking)
                .build();
        }
    }
}
```

## Migration Strategy

### Phase 1: Event Publishing
- Add event publishing to existing cross-aggregate operations
- Implement event handlers for workflow coordination
- Build monitoring and tracing capabilities

### Phase 2: Saga Implementation
- Replace synchronous cross-aggregate calls with saga patterns
- Implement compensation logic for failed workflows
- Add distributed transaction management

### Phase 3: Dapr Workflow Integration
- Migrate complex workflows to Dapr workflow engine
- Implement human task integration
- Add advanced workflow patterns and error handling