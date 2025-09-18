# Quality Assurance (QA) Aggregate Lifecycle Documentation

## Overview

The QA Aggregate manages the complete quality assurance lifecycle in OpenELIS-Global-2, including non-conforming events (NCE), corrective and preventive actions (CAPA), and quality observations. This system ensures compliance with laboratory quality standards and provides comprehensive audit trails for regulatory requirements.

## Aggregate Components

### Core Entities
- **QaEvent** (`org.openelisglobal.qaevent.valueholder.QaEvent`)
  - Root aggregate entity
  - Represents quality assurance incidents
  - Links to samples, analyses, or observations
  
- **NcEvent** (`org.openelisglobal.qaevent.valueholder.NcEvent`)
  - Non-conforming events
  - Tracks deviations from standard procedures
  - Manages CAPA workflows

- **QaObservation** (`org.openelisglobal.qaevent.valueholder.QaObservation`)
  - Quality observations and findings
  - Documents quality issues and resolutions
  - Links to specific laboratory processes

- **SampleQaEvent** (`org.openelisglobal.sampleqaevent.valueholder.SampleQaEvent`)
  - Sample-specific quality events
  - Tracks sample handling issues
  - Manages sample rejection workflows

## State Machine

```mermaid
stateDiagram-v2
    [*] --> Reported : QA Issue Identified
    Reported --> UnderInvestigation : Investigation Started
    UnderInvestigation --> AwaitingAction : Root Cause Found
    AwaitingAction --> ActionInProgress : CAPA Implementation
    ActionInProgress --> AwaitingVerification : Action Completed
    AwaitingVerification --> Closed : Verification Passed
    AwaitingVerification --> ActionInProgress : Verification Failed
    UnderInvestigation --> Reported : Additional Info Needed
    AwaitingAction --> Closed : No Action Required
    Reported --> Closed : False Alarm
    
    note right of Reported : QA Team Notified
    note right of UnderInvestigation : RCA Process
    note right of AwaitingAction : CAPA Planning
    note right of Closed : Metrics Updated
```

## Non-Conforming Event Workflow

```mermaid
stateDiagram-v2
    [*] --> NCEReported : Non-Conformance Detected
    NCEReported --> NCEInvestigating : Investigation Assignment
    NCEInvestigating --> CAPAPlanning : Root Cause Analysis Complete
    CAPAPlanning --> CAPAImplementation : Action Plan Approved
    CAPAImplementation --> CAPAVerification : Implementation Complete
    CAPAVerification --> NCEClosed : Verification Successful
    CAPAVerification --> CAPAImplementation : Rework Required
    NCEInvestigating --> NCEReported : More Information Needed
    NCEReported --> NCEClosed : Determined Not NCE
    
    note right of NCEReported : Immediate Containment
    note right of CAPAPlanning : Preventive Actions
    note right of NCEClosed : Effectiveness Review
```

## Domain Events

### Primary QA Events

| Event | Trigger | State Transition | Audit Trail Location |
|-------|---------|------------------|---------------------|
| **QaEventReported** | Quality issue identified | null → Reported | `history.activity = 'I'` |
| **QaInvestigationStarted** | Investigation assigned | Reported → UnderInvestigation | `history.activity = 'U'` |
| **RootCauseIdentified** | RCA completed | UnderInvestigation → AwaitingAction | `history.activity = 'U'` |
| **CAPAPlanned** | Action plan created | AwaitingAction → ActionInProgress | `history.activity = 'U'` |
| **CAPAImplemented** | Actions completed | ActionInProgress → AwaitingVerification | `history.activity = 'U'` |
| **CAPAVerified** | Verification passed | AwaitingVerification → Closed | `history.activity = 'U'` |
| **QaEventClosed** | Issue resolved | Any → Closed | `history.activity = 'U'` |

### Sample QA Events

| Event | Description | Impact on Sample |
|-------|-------------|------------------|
| **SampleRejected** | Sample fails acceptance criteria | Sample status → Rejected |
| **SampleContainerIssue** | Container problem identified | May require recollection |
| **SampleStorageIssue** | Storage condition violation | Results may be invalidated |
| **SampleLabelingError** | Identification problem | Identity verification required |

### Analysis QA Events

| Event | Description | Impact on Analysis |
|-------|-------------|-------------------|
| **AnalysisRejected** | Results fail validation | Analysis status → Rejected |
| **QCFailure** | Quality control failure | Testing halted pending investigation |
| **EquipmentMalfunction** | Instrument problem | Affected results quarantined |
| **ProcedureDeviation** | Protocol not followed | Results validity questioned |

## Business Rules

### QA Event Creation Rules
1. **Mandatory Fields**: Reporter, date, description, severity
2. **Classification**: Must be categorized by type and severity
3. **Immediate Actions**: Critical issues require immediate containment
4. **Notification**: Stakeholders notified based on severity

### Investigation Rules
1. **Assignment**: Must be assigned to qualified investigator
2. **Timeline**: Investigation deadlines based on severity
3. **Documentation**: All investigation steps must be documented
4. **Root Cause**: Must identify root cause before closure

### CAPA Rules
1. **Approval**: Action plans require management approval
2. **Timeline**: Implementation deadlines must be set
3. **Resources**: Required resources must be identified
4. **Effectiveness**: Actions must be verified for effectiveness

### Closure Rules
1. **Verification**: Independent verification required for critical issues
2. **Documentation**: Complete documentation required
3. **Lessons Learned**: Knowledge sharing for similar issues
4. **Metrics**: Closure updates quality metrics

## Integration Points

### Upstream Dependencies
- **Sample Aggregate**: Sample issues trigger QA events
- **Analysis Aggregate**: Analysis rejections create QA events
- **User Management**: QA personnel and role assignments
- **Equipment Management**: Equipment issues tracked

### Downstream Dependencies
- **Reporting**: QA metrics and regulatory reports
- **Training**: Issues may trigger training requirements
- **Process Improvement**: Trends drive process changes
- **Regulatory Compliance**: Audit trail for inspections

## Workflow Patterns

### Standard QA Investigation
```mermaid
sequenceDiagram
    participant R as Reporter
    participant QA as QA Team
    participant M as Management
    participant I as Investigator
    
    R->>QA: Report Issue
    QA->>QA: Assess Severity
    QA->>I: Assign Investigation
    I->>I: Conduct RCA
    I->>QA: Submit Findings
    QA->>M: Propose CAPA
    M->>QA: Approve Actions
    QA->>QA: Implement CAPA
    QA->>QA: Verify Effectiveness
    QA->>QA: Close Event
```

### Critical Event Response
```mermaid
sequenceDiagram
    participant S as System
    participant QA as QA Team
    participant M as Management
    participant R as Regulatory
    
    S->>QA: Critical Event Detected
    QA->>QA: Immediate Containment
    QA->>M: Escalate to Management
    M->>R: Regulatory Notification
    QA->>QA: Emergency Investigation
    QA->>M: Interim Report
    M->>R: Progress Update
    QA->>QA: Final Resolution
    M->>R: Final Report
```

## Quality Metrics and Analytics

### Key Performance Indicators
- Mean time to closure by severity
- CAPA effectiveness rates
- Recurring issue patterns
- QA event trends by department
- Regulatory compliance scores

### Event-Driven Metrics
```mermaid
graph LR
    A[QaEventReported] --> B[Response Time Metrics]
    C[CAPAImplemented] --> D[Implementation Metrics]
    E[QaEventClosed] --> F[Resolution Metrics]
    G[CAPAVerified] --> H[Effectiveness Metrics]
```

## Audit Trail Coverage

### Comprehensive Tracking
- All status transitions with timestamps
- User attribution for each action
- Document attachments and references
- Cross-reference to affected entities

### Regulatory Compliance
- Complete audit trail for inspections
- Evidence of investigation thoroughness
- CAPA effectiveness documentation
- Trend analysis and preventive actions

### Quality Documentation
```java
// QA audit trail implementation
@Override
@Transactional
public void updateQaEventStatus(QaEvent event, QaEventStatus newStatus) {
    QaEventStatus oldStatus = event.getStatus();
    event.setStatus(newStatus);
    event.setLastModified(new Date());
    
    // Comprehensive audit logging
    auditTrailService.saveHistory(event, "QA_EVENT", "Status Change", 
        String.format("Status changed from %s to %s", oldStatus, newStatus));
}
```

## Event Sourcing Mapping

### Aggregate Root
**QaEvent** serves as the aggregate root with strong consistency boundaries around:
- QA event lifecycle and status progression
- Associated investigations and findings
- CAPA planning and implementation
- Verification and closure activities

### Event Stream Structure
```
QAEventStream-{qaEventId}:
  1. QaEventReported
  2. QaInvestigationStarted
  3. RootCauseIdentified
  4. CAPAPlanned
  5. CAPAImplemented
  6. CAPAVerified
  7. QaEventClosed
```

### Snapshot Strategy
- Snapshot every 20 events
- Include current status, investigation findings, and CAPA status
- Preserve regulatory documentation references

## Technical Implementation Notes

### Current QA Management
```java
// NonConformingEventWorkerImpl.java
public class NonConformingEventWorkerImpl implements NonConformingEventWorker {
    // Status management
    // Investigation workflow
    // CAPA tracking
    // Audit integration
}
```

### Recommended Event Store Schema
```sql
-- Event store table for QA aggregate
CREATE TABLE qa_events (
    aggregate_id VARCHAR(50) NOT NULL,    -- qa_event_id
    sequence_number BIGINT NOT NULL,
    event_type VARCHAR(100) NOT NULL,
    event_data JSONB NOT NULL,
    metadata JSONB,
    severity VARCHAR(20),                 -- for filtering critical events
    regulatory_flag BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP NOT NULL,
    created_by VARCHAR(50) NOT NULL,
    PRIMARY KEY (aggregate_id, sequence_number)
);

-- Index for regulatory queries
CREATE INDEX idx_qa_events_regulatory 
ON qa_events(regulatory_flag, created_at);

-- Index for severity-based queries
CREATE INDEX idx_qa_events_severity 
ON qa_events(severity, created_at);
```

## Regulatory Compliance Features

### FDA 21 CFR Part 820 Compliance
- Quality system procedures
- Corrective and preventive action requirements
- Management responsibility tracking
- Risk management integration

### ISO 15189 Laboratory Requirements
- Quality management system
- Nonconforming work management
- Continual improvement processes
- Management review inputs

### CLIA Compliance
- Quality assurance requirements
- Personnel competency tracking
- Equipment maintenance records
- Proficiency testing compliance

## Migration Strategy

### Phase 1: Enhanced Audit
- Strengthen existing QA audit trails
- Add event publishing capabilities
- Build regulatory reporting views

### Phase 2: Workflow Integration
- Implement Dapr workflows for CAPA processes
- Add automated escalation patterns
- Integrate with external regulatory systems

### Phase 3: Complete Event Sourcing
- Migrate to event-sourced QA aggregate
- Implement real-time compliance monitoring
- Add predictive quality analytics

## Advanced Features

### Risk-Based QA Management
```mermaid
graph TD
    A[Risk Assessment] --> B[QA Event Priority]
    B --> C[Investigation Depth]
    C --> D[CAPA Scope]
    D --> E[Verification Level]
    E --> F[Regulatory Reporting]
```

### Automated Quality Monitoring
- Real-time anomaly detection
- Predictive quality indicators
- Automated escalation triggers
- Continuous compliance monitoring