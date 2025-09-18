# Patient Aggregate Lifecycle Documentation

## Overview

The Patient Aggregate manages patient demographics, identity verification, and relationship tracking in OpenELIS-Global-2. It serves as the foundation for all laboratory activities and maintains comprehensive audit trails for privacy compliance and patient safety.

## Aggregate Components

### Core Entities
- **Patient** (`org.openelisglobal.patient.valueholder.Patient`)
  - Root aggregate entity
  - Core demographics and medical record number
  - Links to Person entity for detailed information
  
- **Person** (`org.openelisglobal.person.valueholder.Person`)
  - Detailed personal information
  - Name components, birth date, gender
  - Contact information and address

- **PatientIdentity** (`org.openelisglobal.patientidentity.valueholder.PatientIdentity`)
  - Multiple patient identifiers
  - National ID, external ID, study numbers
  - Identity verification status

- **ObservationHistory** (`org.openelisglobal.observationhistory.valueholder.ObservationHistory`)
  - Historical patient data changes
  - Sample-specific patient observations
  - Temporal demographic tracking

## State Machine

```mermaid
stateDiagram-v2
    [*] --> Draft : Initial Registration
    Draft --> Provisional : Basic Validation Complete
    Provisional --> Verified : Identity Confirmed
    Verified --> Active : First Sample Collected
    Active --> Inactive : No Activity Period
    Inactive --> Active : Sample Collection Resume
    Active --> Merged : Duplicate Resolution
    Draft --> Canceled : Registration Canceled
    Provisional --> Canceled : Validation Failed
    Merged --> [*]
    Canceled --> [*]
    
    note right of Draft : Minimal Data Entry
    note right of Provisional : Ready for Identity Check
    note right of Verified : Identity Confirmed
    note right of Active : Laboratory Services Available
    note right of Merged : Consolidated Record
```

## Identity Management Workflow

```mermaid
stateDiagram-v2
    [*] --> IdentityEntered : ID Information Provided
    IdentityEntered --> IdentityValidating : Validation Process Started
    IdentityValidating --> IdentityVerified : External Verification Passed
    IdentityValidating --> IdentityFailed : Verification Failed
    IdentityFailed --> IdentityEntered : Correction Needed
    IdentityVerified --> IdentityActive : Identity Confirmed
    IdentityActive --> IdentityChanged : Update Required
    IdentityChanged --> IdentityValidating : Re-validation
    IdentityActive --> IdentityMerged : Duplicate Found
    IdentityMerged --> [*]
```

## Domain Events

### Primary Patient Events

| Event | Trigger | State Transition | Audit Trail Location |
|-------|---------|------------------|---------------------|
| **PatientRegistered** | Initial patient entry | null → Draft | `history.activity = 'I'` |
| **PatientValidated** | Basic validation complete | Draft → Provisional | `history.activity = 'U'` |
| **PatientIdentityVerified** | Identity confirmation | Provisional → Verified | `history.activity = 'U'` |
| **PatientActivated** | First sample collected | Verified → Active | `history.activity = 'U'` |
| **PatientDeactivated** | Inactivity period | Active → Inactive | `history.activity = 'U'` |
| **PatientReactivated** | Resume activity | Inactive → Active | `history.activity = 'U'` |
| **PatientMerged** | Duplicate resolution | Any → Merged | `history.activity = 'U'` |

### Demographics Events

| Event | Description | Privacy Impact |
|-------|-------------|----------------|
| **PatientDemographicsUpdated** | Basic info changed | Audit trail required |
| **PatientNameChanged** | Name modification | Identity verification may be required |
| **PatientAddressUpdated** | Address change | Geographic data updated |
| **PatientContactUpdated** | Phone/email change | Communication preferences affected |
| **PatientBirthDateCorrected** | DOB correction | Age-based validations updated |

### Identity Management Events

| Event | Description | Security Impact |
|-------|-------------|-----------------|
| **PatientIdentityAdded** | New identifier added | Cross-reference integrity |
| **PatientIdentityVerified** | External verification | Trust level increased |
| **PatientIdentityFailed** | Verification failed | Manual review required |
| **PatientIdentityUpdated** | ID information changed | Re-verification triggered |
| **DuplicatePatientDetected** | Potential duplicate found | Merge workflow initiated |

## Business Rules

### Registration Rules
1. **Minimum Data**: Name, birth date, and gender required
2. **Unique Identifiers**: National ID must be unique if provided
3. **Age Validation**: Birth date must be reasonable (not future, not >150 years)
4. **Gender Validation**: Must be valid enumeration value

### Identity Management Rules
1. **Multiple IDs**: Patients can have multiple identity types
2. **Verification Requirement**: Critical IDs require external verification
3. **Uniqueness**: Each identity type must be unique across patients
4. **Format Validation**: IDs must conform to type-specific formats

### Privacy and Security Rules
1. **Audit Trail**: All changes must be logged with user attribution
2. **Data Retention**: Historical data preserved per regulations
3. **Access Control**: Demographic changes require appropriate permissions
4. **Consent Tracking**: Patient consent status tracked for data usage

### Merge and Deduplication Rules
1. **Duplicate Detection**: Algorithmic detection of potential duplicates
2. **Manual Review**: Human verification required before merge
3. **Data Preservation**: All historical data preserved in merge
4. **Audit Trail**: Complete merge history maintained

## Integration Points

### Upstream Dependencies
- **Identity Verification Services**: External ID validation
- **Address Validation**: Geographic data services
- **User Management**: Permission validation for changes

### Downstream Dependencies
- **Sample Aggregate**: Patient demographic changes affect samples
- **Order Processing**: Patient verification status affects orders
- **Reporting**: Demographics used in all laboratory reports
- **Billing**: Patient information drives billing processes

## Privacy and Compliance

### HIPAA Compliance
```mermaid
graph TD
    A[Patient Data Entry] --> B[Access Logging]
    B --> C[Audit Trail Creation]
    C --> D[Data Minimization]
    D --> E[Retention Management]
    E --> F[Secure Deletion]
```

### GDPR Compliance Features
- **Right to Access**: Complete patient data export
- **Right to Rectification**: Controlled demographic updates
- **Right to Erasure**: Secure data deletion workflows
- **Data Portability**: Standardized data export formats
- **Consent Management**: Granular consent tracking

## Workflow Patterns

### Patient Registration Workflow
```mermaid
sequenceDiagram
    participant R as Registrar
    participant S as System
    participant V as Verification Service
    participant A as Administrator
    
    R->>S: Enter Patient Data
    S->>S: Validate Data Format
    S->>V: Verify Identity
    V->>S: Verification Result
    S->>S: Check for Duplicates
    alt Potential Duplicate Found
        S->>A: Flag for Review
        A->>S: Merge Decision
    end
    S->>S: Activate Patient
    S->>R: Registration Complete
```

### Patient Merge Workflow
```mermaid
sequenceDiagram
    participant S as System
    participant A as Administrator
    participant D as Data Manager
    participant Q as QA Team
    
    S->>A: Duplicate Detected
    A->>A: Review Patient Records
    A->>D: Approve Merge
    D->>S: Execute Merge
    S->>S: Update Cross-References
    S->>Q: Notify of Merge
    Q->>Q: Verify Merge Integrity
    Q->>S: Confirm Completion
```

## Audit Trail Coverage

### Comprehensive Patient Tracking
- All demographic changes with timestamps
- Identity verification history
- Merge and deduplication activities
- Access logging for privacy compliance

### ObservationHistory Integration
```java
// Historical patient data tracking
@Override
@Transactional
public void updatePatientDemographics(Patient patient, String changes) {
    // Create observation history entry
    ObservationHistory history = new ObservationHistory();
    history.setPatientId(patient.getId());
    history.setValueType("DEMOGRAPHIC_CHANGE");
    history.setValue(changes);
    history.setLastUpdated(new Date());
    
    observationHistoryService.insert(history);
    
    // Standard audit trail
    auditTrailService.saveHistory(patient, "PATIENT", "Demographics Update", changes);
}
```

## Event Sourcing Mapping

### Aggregate Root
**Patient** serves as the aggregate root with strong consistency boundaries around:
- Patient demographic information
- Identity verification status
- Privacy consent and preferences
- Cross-reference integrity

### Event Stream Structure
```
PatientStream-{patientId}:
  1. PatientRegistered
  2. PatientIdentityAdded (multiple possible)
  3. PatientIdentityVerified
  4. PatientValidated
  5. PatientActivated
  6. PatientDemographicsUpdated (as needed)
  7. PatientMerged | PatientDeactivated
```

### Snapshot Strategy
- Snapshot every 25 events
- Include current demographics, identity status, and active flags
- Preserve privacy consent settings

## Technical Implementation Notes

### Current Patient Management
```java
// PatientServiceImpl.java
@Override
@Transactional
public String insert(Patient patient) {
    // Validation and duplicate checking
    validatePatientData(patient);
    checkForDuplicates(patient);
    
    // Insert with audit trail
    String id = super.insert(patient);
    
    // Identity management
    processPatientIdentities(patient);
    
    return id;
}
```

### Recommended Event Store Schema
```sql
-- Event store table for Patient aggregate
CREATE TABLE patient_events (
    aggregate_id VARCHAR(50) NOT NULL,    -- patient_id
    sequence_number BIGINT NOT NULL,
    event_type VARCHAR(100) NOT NULL,
    event_data JSONB NOT NULL,
    metadata JSONB,
    privacy_level VARCHAR(20) DEFAULT 'STANDARD',
    created_at TIMESTAMP NOT NULL,
    created_by VARCHAR(50) NOT NULL,
    PRIMARY KEY (aggregate_id, sequence_number)
);

-- Index for privacy compliance queries
CREATE INDEX idx_patient_events_privacy 
ON patient_events(privacy_level, created_at);

-- Encrypted storage for sensitive data
CREATE TABLE patient_pii_events (
    aggregate_id VARCHAR(50) NOT NULL,
    sequence_number BIGINT NOT NULL,
    encrypted_data BYTEA NOT NULL,
    encryption_key_id VARCHAR(50) NOT NULL,
    created_at TIMESTAMP NOT NULL,
    PRIMARY KEY (aggregate_id, sequence_number)
);
```

## Metrics and Analytics

### Key Performance Indicators
- Patient registration completion rates
- Identity verification success rates
- Duplicate detection accuracy
- Demographics update frequency
- Privacy compliance scores

### Event-Driven Analytics
```mermaid
graph LR
    A[PatientRegistered] --> B[Registration Metrics]
    C[PatientIdentityVerified] --> D[Verification Metrics]
    E[DuplicatePatientDetected] --> F[Data Quality Metrics]
    G[PatientDemographicsUpdated] --> H[Change Frequency Metrics]
```

## Migration Strategy

### Phase 1: Enhanced Privacy Compliance
- Strengthen audit trails for GDPR/HIPAA compliance
- Add event publishing for demographic changes
- Build privacy-focused read models

### Phase 2: Identity Management Enhancement
- Implement advanced duplicate detection
- Add external identity verification workflows
- Enhance merge capabilities

### Phase 3: Event Store Migration
- Migrate to event-sourced patient aggregate
- Implement privacy-compliant event storage
- Add real-time compliance monitoring

## Advanced Features

### Algorithmic Duplicate Detection
```mermaid
graph TD
    A[New Patient] --> B[Phonetic Matching]
    B --> C[Demographic Similarity]
    C --> D[Identity Cross-Check]
    D --> E[Risk Score Calculation]
    E --> F{Risk Threshold}
    F -->|High| G[Manual Review]
    F -->|Low| H[Auto-Approve]
    G --> I[Merge Decision]
    H --> J[Patient Activated]
```

### Privacy-Preserving Analytics
- Differential privacy for research
- Anonymization for reporting
- Consent-based data usage
- Secure multi-party computation capabilities