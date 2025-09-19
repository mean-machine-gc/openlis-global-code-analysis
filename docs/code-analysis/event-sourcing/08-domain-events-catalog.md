# Domain Events Catalog

## Overview

This comprehensive catalog documents all domain events identified in the OpenELIS-Global-2 system, extracted from the existing audit trail infrastructure and business workflow analysis. Each event represents a significant business occurrence that has been validated against the current system's audit capabilities.

## Event Classification

Events are classified by:
- **Aggregate**: The domain aggregate that owns the event
- **Category**: Business category (Lifecycle, Quality, Integration, etc.)
- **Criticality**: Business impact level (High, Medium, Low)
- **Frequency**: Expected occurrence rate (High, Medium, Low)
- **Audit Coverage**: Current audit trail support (Full, Partial, None)

## Sample Aggregate Events

### Lifecycle Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **SampleRegistered** | New sample entered into system | Sample creation | High | High | Full |
| **STATSampleAlert** | STAT priority sample alert | STAT sample registration | High | Low | Full |
| **SampleTestingStarted** | First analysis created for sample | Analysis creation | High | High | Full |
| **SampleCompleted** | All analyses finished (normal) | Last analysis finalized | High | High | Full |
| **SampleCompletedWithCriticals** | All analyses finished (critical results) | Critical results present | High | Medium | Full |
| **SampleRejected** | Internal sample rejection | QA rejection | High | Low | Full |
| **ExternalSampleRejected** | External referral rejection | External rejection | High | Low | Full |
| **SampleResultsReleased** | Electronic results release | Patient portal update | High | High | Full |
| **SampleResultsPrinted** | Paper/fax results release | Manual delivery | Medium | Medium | Full |
| **SampleRecalled** | Results recall for correction | Post-release error | High | Low | Full |

### Collection and Handling Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **SampleCollected** | Physical sample collection | Collection timestamp | Medium | High | Full |
| **SampleReceived** | Sample received in laboratory | Receipt logging | Medium | High | Full |
| **SampleBarcodeGenerated** | Barcode label created | Label printing | Low | High | Full |
| **SamplePriorityUpdated** | Standard priority change | Priority update | Medium | Medium | Full |
| **SampleEscalatedToSTAT** | Priority escalated to STAT | STAT authorization | High | Low | Full |
| **SampleNoteAdded** | Regular documentation note | Standard annotation | Low | Medium | Full |
| **SampleCriticalNoteAdded** | Critical/safety note | Safety concern | High | Low | Full |
| **SampleStorageAssigned** | Storage location assigned | Storage management | Low | High | Partial |

### Specialized Program Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **SampleAssignedToPathology** | Sample assigned to pathology program | Pathology workflow selection | Medium | Medium | Full |
| **SampleAssignedToIHC** | Sample assigned to immunohistochemistry | IHC program selection | Medium | Low | Full |
| **SampleAssignedToCytology** | Sample assigned to cytology program | Cytology workflow selection | Medium | Medium | Full |
| **SampleAssignedToGeneral** | Sample assigned to general laboratory | General lab workflow | Medium | High | Full |
| **PathologyCaseCreated** | Pathology case initiated | Case registration | High | Medium | Full |
| **GrossExaminationPerformed** | Gross examination completed | Pathology workflow | High | Medium | Full |
| **MicroscopicExaminationStarted** | Microscopic examination begun | Pathology processing | High | Medium | Full |
| **PathologyReportGenerated** | Pathology report created | Report creation | High | Medium | Full |
| **IHCStainingCompleted** | Immunohistochemistry staining done | IHC processing | Medium | Low | Full |
| **IHCInterpretationCompleted** | IHC interpretation finished | IHC results | Medium | Low | Full |
| **CytologySlidePreparation** | Cytology slide prepared | Cytology processing | Medium | Medium | Full |
| **CytologyScreeningCompleted** | Cytology screening finished | Cytology workflow | Medium | Medium | Full |
| **CytologyClassificationAssigned** | Cytology classification determined | Classification system | High | Medium | Full |

### Quality Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **SampleContainerIssue** | Container problem detected | QA inspection | High | Low | Full |
| **SampleLabelingError** | Labeling discrepancy found | Identity verification | High | Low | Full |
| **SampleStorageIssue** | Storage condition violation | Environmental monitoring | Medium | Low | Full |
| **SampleIntegrityCompromised** | Sample integrity questioned | QA assessment | High | Low | Full |

## Analysis Aggregate Events

### Lifecycle Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **AnalysisCreated** | New analysis ordered | Test ordering | High | High | Full |
| **AnalysisPanelCreated** | Analysis panel/profile created | Panel ordering | High | Medium | Full |
| **AnalysisStarted** | Testing process begun | Technician action | Medium | High | Full |
| **AnalysisStartedOnAnalyzer** | Testing started on analyzer | Analyzer initiation | Medium | High | Full |
| **ResultsEntered** | Test results input | Manual/analyzer entry | High | High | Full |
| **CriticalResultsEntered** | Critical/panic values entered | Critical value detection | High | Low | Full |
| **TechnicalValidation** | Technician validates results | Technical approval | High | High | Full |
| **BiologistApproval** | Final professional approval | Biologist review | High | High | Full |
| **AnalysisFinalized** | Results officially released | Final approval | High | High | Full |
| **AnalysisRejected** | Results rejected | Quality rejection | High | Low | Full |
| **AnalysisRejectedCascade** | Analysis rejection with cascade | Cascading rejection | High | Low | Full |
| **AnalysisCanceled** | Testing canceled | Administrative action | Medium | Low | Full |
| **AnalysisRepeated** | Analysis repeated due to issues | Repeat testing | Medium | Low | Full |
| **AnalysisAmended** | Analysis amended post-release | Post-release correction | High | Low | Full |

### Result Management Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **ResultValueChanged** | Result value modified | Data correction | Medium | Medium | Full |
| **ResultValidated** | Result passes validation | Validation rules | High | High | Full |
| **ResultsValidatedWithOverride** | Results validated with override | Override validation | High | Low | Full |
| **ResultFlagged** | Abnormal result detected | Range checking | Medium | Medium | Full |
| **ResultCommented** | Comment added to result | Manual annotation | Low | Medium | Full |
| **ResultCorrected** | Post-release correction | Error correction | High | Low | Full |
| **CriticalValueDetected** | Panic value identified | Critical threshold | High | Low | Full |
| **DeltaCheckFailed** | Delta check validation failed | Historical comparison | Medium | Low | Full |

### Analyzer Integration Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **AnalyzerResultReceived** | Automated result import | Analyzer interface | Medium | High | Full |
| **AnalyzerResultImported** | Result successfully imported | Import completion | Medium | High | Full |
| **AnalyzerResultValidated** | Import validation passed | Interface validation | Medium | High | Full |
| **AnalyzerResultRejected** | Import validation failed | Interface error | Medium | Low | Full |
| **AnalyzerQCFailed** | Analyzer QC check failed | QC validation | High | Low | Full |
| **AnalyzerCalibrationChanged** | QC calibration updated | QC process | Medium | Medium | Partial |
| **AnalyzerMaintenancePerformed** | Maintenance completed | Maintenance log | Low | Low | Partial |

### Reflex Testing Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **ReflexTestTriggered** | Conditional test ordered | Reflex conditions | Medium | Medium | Full |
| **ReflexCascadeTriggered** | Multiple reflex tests ordered | Cascade conditions | Medium | Low | Full |
| **ReflexTestCompleted** | Reflex testing finished | Secondary analysis | Medium | Medium | Full |
| **ReflexRuleEvaluated** | Reflex rule processed | Rule engine | Low | Medium | Partial |

## QA Aggregate Events

### QA Event Lifecycle

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **QaEventReported** | Quality issue identified | Issue detection | High | Medium | Full |
| **CriticalQAEventCreated** | Critical quality issue created | Critical issue detection | High | Low | Full |
| **QaInvestigationStarted** | Investigation begun | Assignment to investigator | High | Medium | Full |
| **InvestigatorAssigned** | Investigator assigned to QA event | Investigation assignment | High | Medium | Full |
| **InvestigationSubmitted** | Investigation report submitted | Investigation completion | High | Medium | Full |
| **InvestigationWithCAPA** | Investigation includes CAPA | CAPA requirement | High | Medium | Full |
| **RootCauseIdentified** | RCA completed | Investigation findings | High | Medium | Full |
| **CAPAPlanned** | Corrective action planned | Action planning | High | Medium | Full |
| **CAPAApproved** | CAPA plan approved | Management approval | High | Medium | Full |
| **CAPAConditionallyApproved** | CAPA conditionally approved | Conditional approval | High | Medium | Full |
| **CAPAImplemented** | Actions completed | Implementation verified | High | Medium | Full |
| **CAPAActionImplemented** | Individual CAPA action completed | Action completion | High | Medium | Full |
| **CAPAActionPartiallyImplemented** | CAPA action partially completed | Partial implementation | Medium | Medium | Full |
| **CAPAVerified** | Effectiveness confirmed | Verification process | High | Medium | Full |
| **QaEventClosed** | Issue resolved | Final closure | High | Medium | Full |
| **QaEventClosedWithConcerns** | Issue closed with concerns | Closure with reservations | High | Low | Full |
| **QaEventEscalated** | Issue escalated | Management escalation | High | Low | Full |
| **QAEventEscalated** | Quality event escalated | Process escalation | High | Low | Full |
| **QAEventExecutiveEscalation** | Executive level escalation | Executive involvement | High | Low | Full |
| **QaEventReopened** | Closed issue reopened | Issue reopening | High | Low | Full |
| **QAEventReopened** | Quality event reopened | Standard reopening | High | Low | Full |
| **QAEventUrgentReopen** | Urgent reopening of QA event | Emergency reopening | High | Low | Full |

### Non-Conforming Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **NonConformingEventReported** | NCE identified | Deviation detection | High | Medium | Full |
| **NCEInvestigationAssigned** | Investigation assigned | NCE workflow | High | Medium | Full |
| **NCECorrectionImplemented** | Immediate correction | Containment action | High | Medium | Full |
| **NCEPreventiveActionPlanned** | Prevention strategy | CAPA planning | Medium | Medium | Full |
| **NCEEffectivenessReviewed** | Action effectiveness | Review process | Medium | Medium | Full |

### Quality Control Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **QCTestPerformed** | Quality control run | QC schedule | Medium | High | Full |
| **QCTestPassed** | QC within limits | QC validation | Medium | High | Full |
| **QCTestFailed** | QC out of limits | QC violation | High | Low | Full |
| **QCCalibrationPerformed** | Instrument calibrated | Calibration schedule | Medium | Medium | Full |
| **QCTrendAnalyzed** | QC trend reviewed | Trend analysis | Low | Low | Partial |

## Patient Aggregate Events

### Patient Lifecycle

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **PatientRegistered** | New patient entered | Patient creation | High | High | Full |
| **PatientValidated** | Basic validation complete | Data validation | Medium | High | Full |
| **PatientIdentityVerified** | Identity confirmed | External verification | High | Medium | Full |
| **PatientActivated** | First service provided | Sample collection | Medium | High | Full |
| **PatientDeactivated** | Account inactive | Inactivity period | Low | Low | Full |
| **PatientReactivated** | Account reactivated | Service resumption | Low | Low | Full |
| **PatientMerged** | Duplicate resolution | Merge process | High | Low | Full |

### Demographics Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **PatientDemographicsUpdated** | Basic info changed | Data modification | Medium | Medium | Full |
| **PatientSensitiveDataUpdated** | Sensitive information updated | Privacy data change | High | Low | Full |
| **PatientNameChanged** | Name modification | Name update | Medium | Low | Full |
| **PatientAddressUpdated** | Address changed | Address update | Low | Medium | Full |
| **PatientContactUpdated** | Contact info changed | Contact update | Low | Medium | Full |
| **PatientBirthDateCorrected** | DOB correction | Date correction | High | Low | Full |
| **PatientGenderUpdated** | Gender information changed | Gender update | Medium | Low | Full |

### Privacy and Special Status Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **PatientPrivacyUpdated** | Privacy settings changed | Privacy modification | High | Low | Full |
| **MinorPatientConsentUpdated** | Minor patient consent updated | Consent management | High | Low | Full |
| **PatientVIPFlagged** | Patient marked as VIP | VIP designation | Medium | Low | Full |
| **PatientHighProfileFlagged** | High profile patient flagged | Special handling | High | Low | Full |
| **PatientDeathRecorded** | Patient death recorded | Death notification | High | Low | Full |
| **PatientDeathLegalCase** | Death with legal implications | Legal case involvement | High | Low | Full |

### Identity Management Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **PatientIdentityAdded** | New identifier added | ID registration | Medium | Medium | Full |
| **PatientNationalIDAdded** | National ID added to patient | National ID registration | High | Medium | Full |
| **PatientIdentityVerified** | External verification | ID verification | High | Medium | Full |
| **PatientIdentityFailed** | Verification failed | Verification error | High | Low | Full |
| **PatientIdentityUpdated** | ID information changed | ID modification | Medium | Low | Full |
| **DuplicatePatientDetected** | Potential duplicate found | Duplicate detection | High | Low | Full |
| **PatientIdentityMerged** | Identity consolidation | Merge completion | High | Low | Full |
| **PatientsMergedWithConflicts** | Patients merged with conflicts | Complex merge process | High | Low | Full |

## Referral Aggregate Events

### Referral Lifecycle

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **ReferralCreated** | External referral initiated | Referral request | High | Medium | Full |
| **UrgentReferralCreated** | Urgent/STAT referral created | Urgent request | High | Low | Full |
| **ReferralDocumentationComplete** | All info gathered | Documentation review | Medium | Medium | Full |
| **ReferralSent** | Sent to external lab | Transmission | High | Medium | Full |
| **ReferralTransmissionFailed** | Transmission to external failed | Communication error | High | Low | Full |
| **ReferralAcknowledged** | External lab confirms | Acknowledgment | Medium | Medium | Full |
| **ReferralPartiallyAccepted** | Partial acceptance by external | Partial processing | Medium | Low | Full |
| **ReferralResultReceived** | External results back | Result reception | High | Medium | Full |
| **ReferralCriticalResultsReceived** | Critical results received | Critical notification | High | Low | Full |
| **ReferralResultReviewed** | Internal review complete | Review process | High | Medium | Full |
| **ReferralResultsQuestioned** | Results questioned/disputed | Quality concern | High | Low | Full |
| **ExternalReferralRejected** | External lab rejects referral | External rejection | High | Low | Full |
| **ReferralCompleted** | Process finished | Final completion | High | Medium | Full |
| **ReferralCanceled** | Referral terminated | Cancellation | Medium | Low | Full |

### FHIR Integration Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **FHIRTaskCreated** | FHIR task resource created | Task creation | Medium | Medium | Full |
| **FHIRTaskSent** | Task sent to external | FHIR transmission | Medium | Medium | Full |
| **FHIRTaskStatusUpdated** | Status change received | External update | Medium | Medium | Full |
| **FHIRResultsReceived** | DiagnosticReport received | Result reception | High | Medium | Full |
| **FHIRResultsMapped** | External results mapped | Data transformation | Medium | Medium | Full |
| **FHIRCommunicationError** | FHIR transmission failed | Communication error | High | Low | Full |

### SLA and Performance Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **ReferralDelayed** | SLA deadline approaching | Timeline monitoring | Medium | Low | Partial |
| **ReferralExpired** | SLA deadline exceeded | SLA violation | High | Low | Partial |
| **ReferralSLAEscalated** | SLA escalation triggered | Timeline violation | High | Low | Full |
| **ReferralFinalEscalation** | Final escalation for referral | Ultimate escalation | High | Low | Full |
| **ReferralResultRejected** | Results fail validation | Quality check | High | Low | Full |
| **ReferralPerformanceTracked** | Performance metrics updated | Metrics calculation | Low | Medium | Partial |
| **ReferralBillingReconciled** | Billing reconciliation complete | Financial reconciliation | Medium | Low | Full |
| **ReferralBillingDisputed** | Billing dispute raised | Financial dispute | Medium | Low | Full |

## Order Aggregate Events

### Order Lifecycle

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **OrderReceived** | HL7 message received | Message reception | High | High | Full |
| **STATOrderReceived** | STAT priority order received | Urgent order reception | High | Low | Full |
| **OrderValidated** | Validation complete | Message validation | High | High | Full |
| **OrderValidationFailed** | Validation process failed | Validation error | High | Low | Full |
| **OrderRejected** | Validation failed | Validation error | High | Low | Full |
| **OrderProcessingStarted** | Processing initiated | Order processing | Medium | High | Full |
| **OrderPatientCreated** | Patient created from order | Patient generation | High | Medium | Full |
| **OrderSampleCreated** | Sample generated | Sample creation | High | High | Full |
| **OrderAmended** | Order modification received | Order amendment | Medium | Low | Full |
| **OrderAmendmentProcessed** | Amendment processing complete | Amendment completion | Medium | Low | Full |
| **OrderRealized** | Fully processed | Processing complete | High | High | Full |
| **OrderCanceled** | Order terminated | Cancellation message | Medium | Low | Full |
| **OrderSTATCanceled** | STAT order canceled | Urgent cancellation | High | Low | Full |
| **OrderProcessingFailed** | Processing error | System error | High | Low | Full |

### HL7 Integration Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **HL7MessageReceived** | Incoming HL7 message | Interface reception | Medium | High | Full |
| **HL7MessageParsed** | Message structure validated | Parser success | Medium | High | Full |
| **HL7AcknowledgmentSent** | ACK message sent | Acknowledgment | Medium | High | Full |
| **HL7ErrorResponse** | NACK message sent | Error response | High | Low | Full |
| **HL7ResultsSent** | Results transmitted | Result transmission | High | High | Full |
| **HL7MessageError** | Message processing error | Interface error | High | Low | Full |

### Priority Management Events

| Event Name | Description | Trigger | Criticality | Frequency | Audit Coverage |
|------------|-------------|---------|-------------|-----------|----------------|
| **StatOrderReceived** | STAT priority order | Priority assignment | High | Low | Full |
| **ASAPOrderReceived** | ASAP priority order | Priority assignment | High | Low | Full |
| **TimedOrderReceived** | Timed collection order | Scheduled order | Medium | Low | Full |
| **FutureStatOrderReceived** | Future STAT order | Scheduled urgent | High | Low | Full |
| **PriorityEscalated** | Priority upgraded | Priority change | Medium | Low | Full |
| **PriorityDeadlineApproaching** | Deadline warning | Timeline monitoring | Medium | Low | Partial |

## Cross-Aggregate Events

### Workflow Coordination Events

| Event Name | Description | Aggregates Involved | Criticality | Frequency | Audit Coverage |
|------------|-------------|-------------------|-------------|-----------|----------------|
| **WorkflowStarted** | Multi-aggregate process begun | All | High | High | Partial |
| **WorkflowCompleted** | Multi-aggregate process finished | All | High | High | Partial |
| **WorkflowFailed** | Multi-aggregate process failed | All | High | Low | Partial |
| **WorkflowCompensated** | Rollback actions taken | All | High | Low | None |

### Integration Events

| Event Name | Description | Aggregates Involved | Criticality | Frequency | Audit Coverage |
|------------|-------------|-------------------|-------------|-----------|----------------|
| **ExternalSystemConnected** | External system online | Order/Referral | Medium | Low | Partial |
| **ExternalSystemDisconnected** | External system offline | Order/Referral | High | Low | Partial |
| **DataSynchronizationStarted** | Sync process begun | Patient/Sample | Medium | Medium | None |
| **DataSynchronizationCompleted** | Sync process finished | Patient/Sample | Medium | Medium | None |

## Event Schema Definitions

### Base Event Structure

```json
{
  "eventId": "uuid",
  "aggregateId": "string",
  "aggregateType": "string",
  "eventType": "string",
  "eventVersion": "string",
  "eventData": "object",
  "metadata": {
    "userId": "string",
    "timestamp": "ISO8601",
    "source": "string",
    "correlationId": "string",
    "causationId": "string"
  }
}
```

### Sample Event Example

```json
{
  "eventId": "550e8400-e29b-41d4-a716-446655440000",
  "aggregateId": "SAMPLE-2023-001234",
  "aggregateType": "Sample",
  "eventType": "SampleRegistered",
  "eventVersion": "1.0",
  "eventData": {
    "accessionNumber": "2023-001234",
    "patientId": "PAT-789",
    "collectionDate": "2023-12-15T10:30:00Z",
    "priority": "ROUTINE",
    "tests": ["CBC", "BMP"]
  },
  "metadata": {
    "userId": "USER-123",
    "timestamp": "2023-12-15T10:35:00Z",
    "source": "sample-service",
    "correlationId": "ORDER-456",
    "causationId": "OrderReceived-789"
  }
}
```

## Event Store Implementation Guidelines

### Partitioning Strategy

```sql
-- Partition by aggregate type and date
CREATE TABLE events (
    event_id UUID PRIMARY KEY,
    aggregate_id VARCHAR(50) NOT NULL,
    aggregate_type VARCHAR(50) NOT NULL,
    sequence_number BIGINT NOT NULL,
    event_type VARCHAR(100) NOT NULL,
    event_data JSONB NOT NULL,
    metadata JSONB NOT NULL,
    created_at TIMESTAMP NOT NULL
) PARTITION BY RANGE (created_at);

-- Create monthly partitions
CREATE TABLE events_2023_12 PARTITION OF events
FOR VALUES FROM ('2023-12-01') TO ('2024-01-01');
```

### Indexing Strategy

```sql
-- Primary access patterns
CREATE INDEX idx_events_aggregate ON events(aggregate_id, sequence_number);
CREATE INDEX idx_events_type ON events(aggregate_type, created_at);
CREATE INDEX idx_events_correlation ON events((metadata->>'correlationId'));

-- Query optimization
CREATE INDEX idx_events_event_type ON events(event_type, created_at);
CREATE INDEX idx_events_user ON events((metadata->>'userId'), created_at);
```

## Migration Roadmap

### Phase 1: Event Publishing (Months 1-2)
- Implement event publishing for high-frequency events
- Build event store infrastructure
- Create basic read models

### Phase 2: Workflow Integration (Months 3-4)
- Add Dapr workflow integration
- Implement saga patterns
- Build cross-aggregate coordination

### Phase 3: Complete Migration (Months 5-6)
- Migrate all aggregates to event sourcing
- Replace JPA persistence
- Implement advanced analytics

### Phase 4: Optimization (Months 7-8)
- Performance tuning
- Advanced read models
- Predictive analytics

## Monitoring and Alerting

### Event Volume Monitoring

```yaml
# Event volume alerts
alerts:
  - name: HighEventVolume
    condition: rate(events_total[5m]) > 1000
    severity: warning
    
  - name: EventStoreDown
    condition: up{job="event-store"} == 0
    severity: critical
    
  - name: EventProcessingDelay
    condition: event_processing_lag_seconds > 60
    severity: warning
```

### Business Metrics

```yaml
# Business process monitoring
metrics:
  - name: sample_processing_time
    type: histogram
    help: Time from sample registration to completion
    
  - name: qa_event_resolution_time
    type: histogram
    help: Time from QA event creation to closure
    
  - name: referral_turnaround_time
    type: histogram
    help: Time from referral creation to result reception
```

This catalog provides a comprehensive foundation for implementing event sourcing in OpenELIS-Global-2, leveraging the existing audit infrastructure while adding the semantic richness needed for modern event-driven architecture.