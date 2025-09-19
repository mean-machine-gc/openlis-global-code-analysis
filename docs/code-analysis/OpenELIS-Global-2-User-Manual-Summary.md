# OpenELIS-Global-2 User Manual Summary

## Overview

This document summarizes the key user workflows and functionalities from the OpenELIS-Global-2 User Manual, providing a comprehensive understanding of the system's actual capabilities and user interface patterns.

## Table of Contents

- [System Overview](#system-overview)
- [User Interface & Navigation](#user-interface--navigation)
- [Core Laboratory Workflow](#core-laboratory-workflow)
- [Order Management](#order-management)
- [Patient Management](#patient-management)
- [Sample & Specimen Management](#sample--specimen-management)
- [Results Entry](#results-entry)
- [Validation & Release](#validation--release)
- [Specialized Programs](#specialized-programs)
- [Quality Assurance](#quality-assurance)
- [Reporting](#reporting)
- [System Features](#system-features)

---

## System Overview

**OpenELIS-Global-2** is a comprehensive Laboratory Information System designed for public health laboratories in low-and-middle income countries. The system manages the complete laboratory workflow from order entry through result delivery.

### Key Characteristics
- **Web-based application** accessible via Chrome browser
- **Multi-language support** (English/French)
- **Role-based access control** with specialized modules
- **Integration capabilities** with electronic health records and analyzers
- **Comprehensive audit trails** for regulatory compliance

---

## User Interface & Navigation

### Main Menu Structure
The system organizes functionality into workflow-based modules:

1. **Order** - Laboratory order management
2. **Patient** - Patient registration and demographics
3. **Non-Conforming Events** - Quality assurance and issue tracking
4. **Workplan** - Test scheduling and organization
5. **Pathology** - Anatomical pathology workflows
6. **Immunohistochemistry** - Specialized immunohistochemistry testing
7. **Cytology** - Cytology specimen processing
8. **Results** - Laboratory result entry
9. **Validations** - Result validation and approval
10. **Reports** - Report generation and management
11. **Admin** - System configuration and user management
12. **Help** - User documentation access

### Navigation Features
- **Hamburger menu** for main navigation
- **Profile icon** for user settings and language preferences
- **Drop-down menus** for module-specific functions
- **Search capabilities** across all major modules
- **Breadcrumb navigation** within workflows

---

## Core Laboratory Workflow

The system follows a **4-phase laboratory workflow**:

### Phase 1: Order Entry
- Electronic order reception
- Manual order creation
- Patient assignment
- Test selection

### Phase 2: Sample Processing
- Sample collection tracking
- Specimen preparation
- Barcode label generation
- Quality control checks

### Phase 3: Testing & Results
- Test execution
- Result entry (multiple methods)
- Quality validation
- Technical review

### Phase 4: Validation & Release
- Result validation
- Final approval
- Report generation
- Result transmission

---

## Order Management

### Order Entry Methods

#### Manual Order Creation
**Workflow: Orders → Add Order**

1. **Patient Search/Registration**
   - Search by Patient ID, National ID, or demographics
   - Create new patient if not found
   - Validate patient information

2. **Program Selection**
   - Choose laboratory program (General, Pathology, Cytology, etc.)
   - Specify clinical diagnosis
   - Enter previous surgery/treatment information

3. **Sample Assignment**
   - Define sample collection date/time
   - Select sample collector
   - Assign tests and panels

4. **Order Finalization**
   - Set laboratory number and priority
   - Specify requester information
   - Define payment status
   - Submit order

#### Electronic Order Processing
**Workflow: Orders → Incoming Orders**

- **FHIR-based integration** with external systems
- **Automatic order validation** and acceptance
- **Search capabilities** by family name, National ID, lab number, patient number
- **Sorting options** for order management

### Order Modification
**Workflow: Orders → Modify Order**

Users can edit:
- Patient information and demographics
- Program and specimen details
- Sample collection information
- Test assignments
- Order metadata (dates, priority, requester)

---

## Patient Management

### Patient Registration
**Workflow: Patient → Add/Modify Patient**

#### Patient Search
- **Multiple search criteria**: Patient ID, National ID, demographics
- **Advanced search options** with partial matching
- **Patient results table** with selection capabilities

#### Patient Information Management
- **Core demographics**: Name, gender, birth date
- **Address hierarchy**: Country, region, district, commune
- **Contact information**: Phone, email
- **Additional identifiers**: Multiple ID types per patient
- **Special status**: VIP flagging, privacy preferences

### Patient Workflows
- **New patient registration** with identity verification
- **Existing patient updates** with change tracking
- **Patient merging** for duplicate resolution
- **Address management** with hierarchical structure

---

## Sample & Specimen Management

### Sample Collection
- **Collection date/time tracking** with validation
- **Collector identification** and assignment
- **Chain of custody** documentation
- **Specimen type classification**

### Sample Tracking
- **Unique accession numbers** for each sample
- **Barcode label generation** for tracking
- **Sample status monitoring** throughout workflow
- **Priority assignment**: ROUTINE, ASAP, STAT, TIMED, FUTURE_STAT

### Quality Control Integration
- **Non-conforming event flagging** with red flag indicators
- **Sample rejection workflows** with reason tracking
- **Quality checkpoint validation** at each stage

---

## Results Entry

### Multiple Entry Methods

#### By Lab Unit
**Workflow: Results → By Unit**
- Select specific laboratory unit
- Display all unreported tests for that unit
- Batch result entry for unit-based workflow

#### By Patient
**Workflow: Results → By Patient**
- Search by patient demographics or ID
- Display all tests for specific patient
- Complete patient result profile

#### By Laboratory Order
**Workflow: Results → By Order**
- Enter specific laboratory order number
- Display all tests for that order
- Order-centric result entry

#### By Range of Order Numbers
**Workflow: Results → By Range of Order Number**
- Define accession number range
- Batch processing for multiple orders
- Efficient high-volume result entry

#### By Test Date
**Workflow: Results → By Test Date**
- Filter by collection or received date
- Test name and status filtering
- Date-based workflow organization

### Result Entry Interface

#### Result Fields
- **Lab Sample Info**: Accession number, sample details
- **Test Date**: Collection and processing dates
- **Analyzer**: Instrument identification
- **Test Name**: Specific test being performed
- **Normal Range**: Reference values for interpretation
- **Accept**: Validation checkbox
- **Results**: Actual test values
- **Current Result**: Previously entered values
- **Notes**: Comments and annotations

#### Result Types
- **Numeric results** with range validation
- **Text results** for qualitative tests
- **Dropdown selections** for coded results
- **Multi-select options** for complex tests

### Quality Indicators
- **Red flag system** for non-conforming samples
- **Normal range validation** with automatic flagging
- **Critical value alerts** for panic values
- **Reference range checking** with validation

---

## Validation & Release

### Validation Workflow
**Workflow: Validations → [By Unit/Patient/Order/Range/Date]**

#### Validation Options
1. **Individual Result Validation**
   - Review each result manually
   - Accept, reject, or request retest
   - Add validation comments

2. **Batch Validation**
   - **Save All Normal**: Validate all results within normal ranges
   - **Save All Results**: Bulk validate all results
   - **Retest All Results**: Bulk reject for retesting

3. **Selective Validation**
   - Choose specific results for validation
   - Mixed accept/reject for complex cases
   - Partial validation workflows

#### Validation Fields
- **Technical review** by qualified personnel
- **Comment addition** for providers and internal use
- **Validation status tracking** throughout process
- **Final approval** before result release

### Result Release
- **Automatic report generation** upon validation
- **Electronic result transmission** to requesting systems
- **Paper report printing** for manual delivery
- **Result modification tracking** with audit trails

---

## Specialized Programs

### Pathology Module
**Workflow: Pathology → [Various pathology functions]**

#### Pathology Case Creation
1. **Case registration** with patient assignment
2. **Specimen information** and collection details
3. **Clinical history** and diagnostic information
4. **Gross examination** documentation
5. **Microscopic examination** and interpretation
6. **Final diagnosis** and reporting

#### Report Management
- **Report upload** from external systems
- **System-generated reports** from templates
- **PDF report generation** with standardized formatting
- **Report revision** and amendment tracking

### Immunohistochemistry Module
**Workflow: Immunohistochemistry → [IHC functions]**

#### IHC Test Processing
1. **Program selection** for immunohistochemistry
2. **Specimen details**: Nature, site, procedure
3. **Clinical diagnosis** and treatment history
4. **Sample assignment** with IHC-specific tests
5. **Test execution** and result capture
6. **Validation and reporting**

### Cytology Module
**Workflow: Cytology → [Cytology functions]**

#### Cytology Workflow
1. **Cytology program selection**
2. **Specimen collection** and preparation details
3. **Clinical information** and screening history
4. **Microscopic examination** with standardized reporting
5. **Result categorization** using classification systems
6. **Quality assurance** and validation

#### Cytology Result Categories
- **Specimen adequacy assessment**
- **General categorization** (Normal, Abnormal, etc.)
- **Epithelial cell abnormalities** with subcategories
- **Organism identification** and reporting
- **Reactive cellular changes** documentation

---

## Quality Assurance

### Non-Conforming Events (NCE)
**Workflow: Non-Conforming Events → [NCE functions]**

#### NCE Reporting Process
1. **Event identification** and classification
2. **Problem description** with detailed documentation
3. **Immediate actions** and containment measures
4. **Root cause analysis** and investigation
5. **Corrective actions** and implementation
6. **Effectiveness verification** and closure

#### NCE Management
- **Event categorization** by type and severity
- **Investigation assignment** to qualified personnel
- **Timeline tracking** with due dates
- **Status monitoring** throughout lifecycle
- **Resolution documentation** with evidence

#### Quality Indicators
- **Red flag system** visible throughout interface
- **Sample blocking** for quality issues
- **Workflow integration** with all modules
- **Audit trail maintenance** for compliance

### CAPA (Corrective and Preventive Actions)
- **Action planning** with timeline and responsibility
- **Implementation tracking** with progress monitoring
- **Effectiveness evaluation** and verification
- **Continuous improvement** integration

---

## Reporting

### Report Generation
**Workflow: Reports → [Various report types]**

#### Report Types
- **Patient reports** with complete test results
- **Laboratory workload** and productivity reports
- **Quality metrics** and compliance reports
- **Statistical reports** for management
- **Custom reports** with flexible parameters

#### Report Features
- **Multi-format output** (PDF, electronic)
- **Template-based generation** for consistency
- **Parameter selection** for customization
- **Batch report generation** for efficiency
- **Report distribution** via multiple channels

---

## System Features

### Security & Access Control
- **User authentication** with role-based access
- **Password management** with security requirements
- **Session management** with timeout controls
- **Audit logging** for all user actions

### Integration Capabilities
- **FHIR R4 support** for healthcare interoperability
- **HL7 v2.5.1** for laboratory automation
- **Analyzer interfaces** with plugin architecture
- **Electronic health record** integration
- **External laboratory** referral systems

### Data Management
- **Comprehensive audit trails** for all changes
- **Data validation** with business rule enforcement
- **Backup and recovery** capabilities
- **Data export** and import functions
- **Archival management** for long-term storage

### Performance Features
- **Batch processing** for high-volume operations
- **Search optimization** with multiple criteria
- **Workflow automation** to reduce manual steps
- **User interface responsiveness** for efficient operation

---

## Key User Pain Points Identified

Based on the manual analysis, several performance and usability challenges are evident:

### Workflow Inefficiencies
1. **Multi-step navigation** requiring multiple page loads
2. **Complex search processes** with limited filtering
3. **Manual data entry** repetition across modules
4. **Limited batch operations** for routine tasks

### Performance Bottlenecks
1. **Patient search delays** mentioned implicitly in multiple search methods
2. **Result entry complexity** requiring multiple specialized interfaces
3. **Validation workflow steps** with manual iteration
4. **Report generation** with limited real-time capabilities

### Integration Challenges
1. **Electronic order processing** requiring manual acceptance
2. **Analyzer result import** needing manual triggering
3. **External system coordination** with manual oversight
4. **Data synchronization** across modules

---

## Modernization Opportunities

The user manual reveals significant opportunities for improvement through event sourcing and CQRS implementation:

### Performance Improvements
- **Real-time patient search** with instant results
- **Streamlined result entry** with intelligent defaults
- **Automated validation workflows** with exception handling
- **Fast report generation** with pre-computed analytics

### Workflow Optimization
- **Single-page applications** reducing navigation complexity
- **Intelligent batch operations** for routine tasks
- **Automated quality checks** with real-time alerts
- **Seamless integration** with external systems

### User Experience Enhancement
- **Responsive interfaces** with modern UI patterns
- **Contextual information** reducing manual lookups
- **Workflow guidance** with intelligent assistance
- **Real-time collaboration** features for team coordination

---

## Conclusion

The OpenELIS-Global-2 User Manual reveals a sophisticated, comprehensive laboratory information system with proven workflows serving laboratories worldwide. The system demonstrates:

- **Mature business logic** covering all aspects of laboratory operations
- **Comprehensive quality assurance** integration throughout workflows
- **Flexible configuration** supporting diverse laboratory environments
- **Robust integration capabilities** with healthcare ecosystems

The documented workflows validate our event sourcing and CQRS modernization strategy, confirming that our technical approach will preserve proven functionality while delivering significant performance improvements for real-world laboratory operations.

*Document created: January 2025*  
*Based on: OpenELIS-Global-2 User Manual analysis*  
*Total workflow coverage: 10+ specialized modules with 50+ user scenarios*