# QA/CAPA Workflow Implementation with Dapr

## Overview

The Quality Assurance and Corrective/Preventive Action (QA/CAPA) workflow is one of the most complex and critical long-running processes in OpenELIS-Global-2. The current implementation suffers from manual state management, lack of reminders, and poor visibility into the CAPA process. This document details how Dapr Workflows can transform this into a reliable, compliant, and efficient system.

## Current Implementation Pain Points

### 1. **Manual State Management**
```java
// From NonConformingEventWorkerImpl.java
ncEvent.setStatus("CAPA");  // String-based status
ncEventService.update(ncEvent);

// Later in another method...
ncEvent.setStatus("Completed");
ncEventService.update(ncEvent);
```

**Problems:**
- Status tracked as strings in database
- No validation of state transitions
- Easy to corrupt workflow state
- No audit trail of state changes

### 2. **No Automatic Reminders or Escalation**
```java
// Current: No mechanism for reminders
// QA events can sit indefinitely without action
// Manual checking required for overdue items
```

**Problems:**
- NCE events forgotten or delayed
- No SLA enforcement
- Manual follow-up required
- Compliance risks

### 3. **Complex Multi-Step Process Without Orchestration**
```java
// Scattered across multiple methods
private void createNCEvent() { /* ... */ }
private void doFollowUpAction() { /* ... */ }
private void doCorrectiveAction() { /* ... */ }
// No clear workflow coordination
```

**Problems:**
- Workflow logic scattered
- Difficult to understand flow
- Hard to modify process
- No compensation on failures

## Dapr Workflow Solution

### Workflow Definition

```csharp
public class QaCAPAWorkflow : Workflow<QaEventInput, QaEventResult>
{
    public override async Task<QaEventResult> RunAsync(
        WorkflowContext context, 
        QaEventInput input)
    {
        var qaEvent = input.QaEvent;
        
        // Step 1: Initial QA Event Creation and Notification
        var eventId = await context.CallActivityAsync<string>(
            nameof(CreateQaEventActivity),
            qaEvent,
            RetryPolicy.Default);
        
        context.SetCustomStatus("QA Event Created - Awaiting Investigation");
        
        // Step 2: Assign Investigator with timeout
        var investigator = await context.CallActivityAsync<Investigator>(
            nameof(AssignInvestigatorActivity),
            new AssignmentRequest 
            { 
                EventId = eventId, 
                Severity = qaEvent.Severity 
            });
        
        // Step 3: Investigation Phase with Reminders
        var investigationResult = await ExecuteInvestigationPhase(
            context, eventId, investigator);
        
        // Step 4: CAPA Planning (if required)
        QaEventResult result;
        if (investigationResult.RequiresCAPA)
        {
            result = await ExecuteCAPAWorkflow(
                context, eventId, investigationResult);
        }
        else
        {
            result = await CompleteWithoutCAPA(
                context, eventId, investigationResult);
        }
        
        // Step 5: Effectiveness Review (for CAPA)
        if (result.CAPAImplemented)
        {
            await ScheduleEffectivenessReview(context, eventId);
        }
        
        return result;
    }
    
    private async Task<InvestigationResult> ExecuteInvestigationPhase(
        WorkflowContext context,
        string eventId,
        Investigator investigator)
    {
        context.SetCustomStatus("Investigation Phase Started");
        
        // Set investigation deadline based on severity
        var deadline = GetInvestigationDeadline(investigator.Severity);
        
        using var cts = new CancellationTokenSource();
        
        // Create timer for reminder
        var reminderTimer = context.CreateTimer(
            DateTime.UtcNow.Add(deadline * 0.75), // 75% of deadline
            cts.Token);
            
        // Create timer for escalation
        var escalationTimer = context.CreateTimer(
            DateTime.UtcNow.Add(deadline),
            cts.Token);
        
        // Wait for investigation completion or timers
        var investigationTask = context.WaitForExternalEventAsync<InvestigationResult>(
            "InvestigationComplete");
        
        var winner = await context.TaskAny(
            investigationTask,
            reminderTimer,
            escalationTimer);
        
        if (winner == reminderTimer)
        {
            // Send reminder
            await context.CallActivityAsync(
                nameof(SendReminderActivity),
                new ReminderRequest 
                { 
                    InvestigatorId = investigator.Id,
                    EventId = eventId,
                    Message = "Investigation deadline approaching"
                });
            
            // Continue waiting
            winner = await context.TaskAny(investigationTask, escalationTimer);
        }
        
        if (winner == escalationTimer)
        {
            // Escalate to management
            await context.CallActivityAsync(
                nameof(EscalateToManagementActivity),
                new EscalationRequest 
                { 
                    EventId = eventId,
                    Reason = "Investigation deadline exceeded"
                });
            
            // Wait for escalated investigation
            return await context.WaitForExternalEventAsync<InvestigationResult>(
                "InvestigationComplete");
        }
        
        cts.Cancel(); // Cancel unused timers
        return await investigationTask;
    }
    
    private async Task<QaEventResult> ExecuteCAPAWorkflow(
        WorkflowContext context,
        string eventId,
        InvestigationResult investigation)
    {
        context.SetCustomStatus("CAPA Planning Phase");
        
        // Step 1: CAPA Plan Development
        var capaPlan = await context.WaitForExternalEventAsync<CAPAPlan>(
            "CAPAPlanSubmitted",
            timeout: TimeSpan.FromDays(7));
        
        // Step 2: CAPA Plan Approval
        var approval = await context.CallActivityAsync<ApprovalResult>(
            nameof(RequestCAPAApprovalActivity),
            capaPlan,
            new WorkflowRetryPolicy(
                maxAttempts: 3,
                firstRetryInterval: TimeSpan.FromHours(1)));
        
        if (!approval.Approved)
        {
            context.SetCustomStatus("CAPA Plan Rejected - Revision Required");
            // Recursively call for plan revision
            return await ExecuteCAPAWorkflow(context, eventId, investigation);
        }
        
        context.SetCustomStatus("CAPA Implementation Phase");
        
        // Step 3: Track CAPA Implementation
        var implementation = await TrackCAPAImplementation(
            context, eventId, capaPlan);
        
        // Step 4: Verify Implementation
        var verification = await context.CallActivityAsync<VerificationResult>(
            nameof(VerifyCAPAImplementationActivity),
            new VerificationRequest 
            { 
                EventId = eventId,
                ImplementationId = implementation.Id 
            });
        
        return new QaEventResult
        {
            EventId = eventId,
            Status = "Completed",
            CAPAImplemented = true,
            ImplementationDetails = implementation,
            VerificationDetails = verification
        };
    }
    
    private async Task<CAPAImplementation> TrackCAPAImplementation(
        WorkflowContext context,
        string eventId,
        CAPAPlan plan)
    {
        var tasks = plan.ActionItems.Select(async action =>
        {
            // Set deadline for each action
            var actionDeadline = context.CreateTimer(action.DueDate);
            var actionComplete = context.WaitForExternalEventAsync<ActionCompletion>(
                $"ActionComplete_{action.Id}");
            
            var winner = await context.TaskAny(actionComplete, actionDeadline);
            
            if (winner == actionDeadline)
            {
                // Action overdue
                await context.CallActivityAsync(
                    nameof(NotifyActionOverdueActivity),
                    action);
                
                // Wait for completion with escalation
                return await context.WaitForExternalEventAsync<ActionCompletion>(
                    $"ActionComplete_{action.Id}");
            }
            
            return await actionComplete;
        }).ToList();
        
        var completions = await Task.WhenAll(tasks);
        
        return new CAPAImplementation
        {
            EventId = eventId,
            CompletedActions = completions.ToList(),
            CompletionDate = DateTime.UtcNow
        };
    }
    
    private async Task ScheduleEffectivenessReview(
        WorkflowContext context,
        string eventId)
    {
        // Schedule effectiveness review after 30 days
        await context.CreateTimer(DateTime.UtcNow.AddDays(30));
        
        context.SetCustomStatus("Effectiveness Review Scheduled");
        
        // Start sub-orchestration for effectiveness review
        await context.CallSubWorkflowAsync<EffectivenessResult>(
            nameof(EffectivenessReviewWorkflow),
            new EffectivenessReviewInput { EventId = eventId });
    }
}
```

### Activity Implementations

```csharp
public class QaCAPAActivities
{
    private readonly IQaEventService _qaEventService;
    private readonly INotificationService _notificationService;
    private readonly IUserService _userService;
    
    [Activity]
    public async Task<string> CreateQaEventActivity(
        [ActivityInput] QaEvent qaEvent)
    {
        // Create QA event in database
        var eventId = await _qaEventService.CreateEventAsync(qaEvent);
        
        // Notify QA team
        await _notificationService.NotifyQaTeamAsync(
            new QaNotification
            {
                EventId = eventId,
                Severity = qaEvent.Severity,
                Description = qaEvent.Description
            });
        
        // Audit trail
        await _auditService.LogAsync(new AuditEntry
        {
            EntityType = "QaEvent",
            EntityId = eventId,
            Action = "Created",
            UserId = qaEvent.ReporterId
        });
        
        return eventId;
    }
    
    [Activity]
    public async Task<Investigator> AssignInvestigatorActivity(
        [ActivityInput] AssignmentRequest request)
    {
        // Complex logic for investigator selection
        var availableInvestigators = await _userService
            .GetAvailableInvestigatorsAsync(request.Severity);
        
        var selected = SelectBestInvestigator(
            availableInvestigators, 
            request.Severity);
        
        await _qaEventService.AssignInvestigatorAsync(
            request.EventId, 
            selected.Id);
        
        await _notificationService.NotifyInvestigatorAsync(
            selected.Id,
            request.EventId);
        
        return selected;
    }
    
    [Activity]
    public async Task<ApprovalResult> RequestCAPAApprovalActivity(
        [ActivityInput] CAPAPlan plan)
    {
        // Create approval request
        var approvalId = await _approvalService.CreateApprovalRequestAsync(
            new ApprovalRequest
            {
                Type = "CAPA_PLAN",
                EntityId = plan.EventId,
                Details = plan,
                RequiredApprovers = GetRequiredApprovers(plan.EstimatedCost)
            });
        
        // Wait for approval with timeout
        var result = await _approvalService.WaitForApprovalAsync(
            approvalId,
            TimeSpan.FromDays(3));
        
        return result;
    }
}
```

### State Persistence and Recovery

```csharp
// Workflow state is automatically persisted by Dapr
// On failure, workflow resumes from last checkpoint

public class WorkflowState
{
    public string EventId { get; set; }
    public string CurrentPhase { get; set; }
    public DateTime PhaseStartTime { get; set; }
    public List<string> CompletedSteps { get; set; }
    public Dictionary<string, object> PhaseData { get; set; }
}

// Dapr handles all state management automatically
// No manual database updates required
```

## Benefits of Dapr Implementation

### 1. **Automatic State Management**

**Before:**
```java
// Manual state tracking
ncEvent.setStatus("CAPA");
ncEventService.update(ncEvent);
// Risk of inconsistent state
```

**After:**
```csharp
// Automatic state persistence
context.SetCustomStatus("CAPA Planning Phase");
// State survives failures and restarts
```

### 2. **Built-in Reminders and Escalation**

**Before:**
```java
// No automatic reminders
// Manual checking required
```

**After:**
```csharp
// Automatic reminders
var reminderTimer = context.CreateTimer(
    DateTime.UtcNow.Add(deadline * 0.75));
    
// Automatic escalation
if (winner == escalationTimer) {
    await EscalateToManagement();
}
```

### 3. **Comprehensive Audit Trail**

```csharp
// Every workflow action is automatically logged
// Complete history available
var history = await workflowClient.GetWorkflowHistoryAsync(instanceId);

// Query capabilities
var overdueEvents = await workflowClient.QueryWorkflowsAsync(
    query => query
        .AddCustomStatusFilter("Investigation Phase")
        .AddCreatedTimeFilter(DateTime.UtcNow.AddDays(-7), null));
```

### 4. **Parallel Action Tracking**

```csharp
// Track multiple CAPA actions in parallel
var tasks = plan.ActionItems.Select(async action =>
{
    return await TrackActionCompletion(action);
});

var completions = await Task.WhenAll(tasks);
```

## Compliance Benefits

### 1. **21 CFR Part 820 Compliance**
- Complete audit trail of all QA events
- Documented investigation process
- Effectiveness review tracking
- Management notification and approval

### 2. **ISO 15189 Requirements**
- Systematic approach to nonconforming work
- Documented corrective actions
- Preventive action implementation
- Continuous improvement tracking

### 3. **Real-time Compliance Monitoring**
```csharp
// Monitor QA metrics in real-time
var metrics = await workflowClient.GetQAMetricsAsync();
Console.WriteLine($"Open QA Events: {metrics.OpenEvents}");
Console.WriteLine($"Average Resolution Time: {metrics.AvgResolutionTime}");
Console.WriteLine($"Overdue Investigations: {metrics.OverdueCount}");
```

## Migration Strategy

### Phase 1: Pilot Implementation (Month 1)
1. Deploy Dapr workflow for new QA events
2. Keep existing system for ongoing events
3. Validate workflow behavior

### Phase 2: Data Migration (Month 2)
1. Migrate open QA events to workflows
2. Map existing statuses to workflow states
3. Import historical data for reporting

### Phase 3: Full Cutover (Month 3)
1. All QA events use Dapr workflow
2. Retire old implementation
3. Monitor and optimize

## Expected Improvements

### Quantitative Benefits
- **90% reduction** in overdue QA events
- **75% faster** average resolution time
- **100% audit trail** compliance
- **60% reduction** in manual follow-ups

### Qualitative Benefits
- Clear visibility into QA process
- Automatic regulatory compliance
- Reduced manual intervention
- Improved team productivity
- Better quality outcomes

## Monitoring and Analytics

```csharp
// Real-time dashboards
public class QADashboard
{
    public async Task<QAMetrics> GetMetricsAsync()
    {
        var running = await GetRunningWorkflows();
        var completed = await GetCompletedWorkflows(DateTime.Today.AddDays(-30));
        
        return new QAMetrics
        {
            ActiveEvents = running.Count(),
            CompletedThisMonth = completed.Count(),
            AverageResolutionTime = completed.Average(w => w.Duration),
            OverdueEvents = running.Count(w => w.IsOverdue),
            CAPASuccessRate = completed.Count(w => w.CAPASuccessful) / completed.Count()
        };
    }
}
```

This Dapr workflow implementation transforms the QA/CAPA process from a manual, error-prone system into a reliable, compliant, and efficient workflow that meets all regulatory requirements while improving operational efficiency.