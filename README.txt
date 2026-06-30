@startuml
skinparam TypeHierarchyBackgroundColor #EDF2F7
skinparam BackgroundColor #FFFFFF
skinparam ArrowColor #2D3748
skinparam BorderColor #1A202C

title Digital-SEA\nCR workflow - Class Layout

() "Workflow Start" as Start
() "Workflow End" as End

class "«Expression Robot»\nSet Product Manager as Process Initiator" as processInitator {
    + cr : WTChangeRequest2
    + prod : PDMLinkProduct
    + team : ContainerTeam
    + principals : ArrayList
    + pref : WTPrincipalReference

    # ContainerTeamHelper.service.getContainerTeam()
    # team.getAllPrincipalsForTarget()
    # Role.toRole("PRODUCT MANAGER")
    # wfProcess.setCreator()
}

class "«Expression Robot»\nSet Workflow Variables" as workflowVar {
    + coenpiCrObject : WTChangeRequest2
    + obj : PersistableAdapter
    + coenpiCrName : String
    + coenpiCrNumber : String
    + coenpiCrPriority : String
    + pfamNumber : String

    # coenpiCrObject.getName()
    # coenpiCrObject.getNumber()
    # obj.load(String)
    # obj.get(String)
}

Start --> processInitator 
processInitator --> workflowVar 

class "«Assigned Activity»\nSubmit AM Part Design Request" as submitCR {
    + thisActivity : wt.workflow.engine.WfActivity
    + thisChangeObj : wt.change2.WTChangeRequest2
    + activityName : String
    + pboNumber : String
    + pboName : String
    + coeNpiCrPriority : String

    # thisActivity.getName() : String
    # thisActivity.setName(String) : void
    # thisChangeObj.getNumber() : String
    # thisChangeObj.getName() : String
    # PersistenceHelper.manager.save(Persistable) : Persistable
}

class "«Expression Robot»\nGet Linked CRP" as getLinkedCRP {
    + amCoenpiCR : WTChangeRequest2
    + rf : ReferenceFactory
    + ref : WTReference
    + tiCheck : TypeIdentifier
    + qr : QueryResult
    + doc : WTDocument
    + linkedCRP : WTDocument

    # rf.getReference(Persistable)
    # ClientTypedUtility.getTypeIdentifier(String)
    # ConfigurableLinkHelper.service.getOtherSideObjectsFromLink()
    # qr.hasMoreElements()
    # qr.nextElement()
}
class "«Expression Robot»\nSync CRP State with CR" as syncCRP {
    + cr : WTChangeRequest2
    + targetState : State
    + linkedCRP : LifeCycleManaged

    # PersistenceHelper.manager.refresh()
    # cr.getLifeCycleState()
    # LifeCycleHelper.service.setLifeCycleState()
}

class "«Assigned Activity»\nDownload Data for Feasibility Review" as downloadData {
    + thisActivity : wt.workflow.engine.WfActivity
    + process : wt.workflow.engine.WfProcess
    + pfam : Object
    + priority : Object
    + activityName : String
    + thisChangeObj : wt.change2.WTChangeRequest2

    # thisActivity.getParentProcess() : WfProcess
    # process.getContext().getValue(String) : Object
    # thisActivity.getName() : String
    # thisChangeObj.getName() : String
    # thisActivity.setName(String) : void
    # PersistenceHelper.manager.modify(Persistable) : Persistable
}
workflowVar --> submitCR
submitCR --> getLinkedCRP
getLinkedCRP --> syncCRP 
syncCRP -right-> downloadData 


package "Not Printable" {
class "«Assigned Activity»\nAssociate Justification Report" as associateJust {
    + thisActivity : wt.workflow.engine.WfActivity
    + process : wt.workflow.engine.WfProcess
    + pfam : Object
    + priority : Object
    + activityName : String
    + thisChangeObj : wt.change2.WTChangeRequest2
    -- Methods Used --
    # thisActivity.getParentProcess() : WfProcess
    # process.getContext().getValue(String) : Object
    # thisActivity.getName() : String
    # thisChangeObj.getName() : String
    # thisActivity.setName(String) : void
    # PersistenceHelper.manager.modify(Persistable) : Persistable
}
class "«Expression Robot»\nCheck for Justification Report" as checkForJust {
    + amCoenpiCR : WTChangeRequest2
    + rf : ReferenceFactory
    + ref : WTReference
    + tiCheck : TypeIdentifier
    + qr : QueryResult
    + doc : WTDocument
    + justificationReportFound : boolean
    + linkedJustificationReport : WTDocument
    # rf.getReference(Persistable)
    # ClientTypedUtility.getTypeIdentifier(String)
    # ConfigurableLinkHelper.service.getOtherSideObjectsFromLink()
    # qr.hasMoreElements()
    # qr.nextElement()
}
class "«Expression Robot»\nJustification Report not attached" as JRnotAttached {
    + thisActivity : wt.workflow.engine.WfActivity
    + process : wt.workflow.engine.WfProcess
    + pfam : Object
    + priority : Object
    + activityName : String
    + thisChangeObj : wt.change2.WTChangeRequest2
    # thisActivity.getParentProcess()
    # process.getContext().getValue()
    # thisActivity.getName()
    # thisChangeObj.getName()
    # thisActivity.setName()
    # PersistenceHelper.manager.modify()
}
class "«Assigned Activity»\nReview Justification Report" as reviewJR { 
    + thisActivity : wt.workflow.engine.WfActivity
    + process : wt.workflow.engine.WfProcess
    + pfam : Object
    + priority : Object
    + activityName : String
    + thisChangeObj : wt.change2.WTChangeRequest2
    # thisActivity.getParentProcess()
    # process.getContext().getValue()
    # thisActivity.getName()
    # thisChangeObj.getName()
    # thisActivity.setName()
    # PersistenceHelper.manager.modify()
}
class "«Expression Robot»\nSet Justification Report State Approved" as setJRApproved { 
    + approvedState : State
    + justificationDoc : LifeCycleManaged
    + linkedJustificationReport : Object
    # State.toState("APPROVED")
    # LifeCycleHelper.service.setLifeCycleState()
}
class "«Notification Robot»\nPLM Alerts ALL" as AlertALL { 
    + process : WfProcess
    + creator : WTPrincipal
    + emailSubject : String
    + emailBody : String
    + notificationTemplate : String
    # self.getProcess()
    # process.getCreator()
    # NotificationHelper.manager.sendNotification()
}
class "«Set State Robot»\nSet State Rejected" as SetStateRejected { 
    + primaryBusinessObject : LifecycleManaged
    + targetState : State = REJECTED
    # LifeCycleHelper.service.setLifeCycleState()
}
class "«Expression Robot»\nSync CRP State with CR" as syncCRPwithCR { 
    + cr : WTChangeRequest2
    + targetState : State
    + linkedCRP : LifeCycleManaged
    # PersistenceHelper.manager.refresh()
    # cr.getLifeCycleState()
    # LifeCycleHelper.service.setLifeCycleState()
}
class "«Assigned Activity»\nJustification Report Update" as JRUpdate { 
    + thisActivity : WfActivity
    + process : WfProcess
    + pfam : Object
    + priority : Object
    + activityName : String
    + thisChangeObj : WTChangeRequest2
    # thisActivity.getParentProcess()
    # process.getContext().getValue()
    # thisActivity.getName()
    # thisChangeObj.getName()
    # thisActivity.setName()
    # PersistenceHelper.manager.modify()
}

associateJust --> checkForJust
checkForJust -right-> reviewJR #line:green;line.bold;text:green : JR Attached
checkForJust --> JRnotAttached #line:red;line.bold;text:red : JR Not Attached
JRnotAttached --> checkForJust #line.bold;text:red : Loop
reviewJR --> setJRApproved #line:red;line.bold;text:red : Accept
setJRApproved --> AlertALL 
AlertALL --> SetStateRejected 
SetStateRejected --> syncCRPwithCR 
syncCRPwithCR --> End
reviewJR -up-> JRUpdate #line:green;line.bold;text:green : Reject
JRUpdate --> reviewJR #line:red;line.bold;text:red : Not Printable
}

class "«Expression Robot»\nset PFAM Number on CR" as setPFAM { 
    + coenNpiCrObject : WTChangeRequest2
    + obj : PersistableAdapter
    + pfamNumber : String
    # obj.load(String)
    # obj.set(String, Object)
    # obj.apply()
    # PersistenceHelper.manager.modify()
}

class "«Assigned Activity»\nCreate and associate OEM model" as associateOEM {
    + thisActivity : WfActivity
    + activityName : String
    + thisChangeObj : WTChangeRequest2
    + pboNumber : String
    + pboName : String
    # thisActivity.getName()
    # thisChangeObj.getNumber()
    # thisChangeObj.getName()
    # thisActivity.setName()
    # PersistenceHelper.manager.save()
}
class "«Notification Robot»\nBegin work on drawng packet" as beginWork { 
    + process : WfProcess
    + creator : WTPrincipal
    + emailSubject : String
    + emailBody : String
    + notificationTemplate : String
    # self.getProcess()
    # process.getCreator()
    # NotificationHelper.manager.sendNotification()
}

downloadData --> associateJust #line:red;line.bold;text:red : Not Printable
downloadData -right-> setPFAM #line:green;line.bold;text:green : Printable
JRUpdate -up-> setPFAM #line:green;line.bold;text:green : Printable
setPFAM -right-> associateOEM 
setPFAM -up-> beginWork 

class "«Assigned Activity»\nAssociate OEM Model to CR" as associateOEMToCR {
    + thisActivity : WfActivity
    + process : WfProcess
    + pfam : Object
    + priority : Object
    + activityName : String
    + thisChangeObj : WTChangeRequest2
    # thisActivity.getParentProcess()
    # process.getContext().getValue()
    # thisActivity.getName()
    # thisChangeObj.getName()
    # thisActivity.setName()
    # PersistenceHelper.manager.modify()
}
class "«Notification Robot»\nPLM Alerts COE" as alertCOE { 
    + process : WfProcess
    + creator : WTPrincipal
    + emailSubject : String
    + emailBody : String
    + notificationTemplate : String
    # self.getProcess()
    # process.getCreator()
    # NotificationHelper.manager.sendNotification()
}
associateOEM -down-> associateOEMToCR : OEM Model Uploaded
associateOEM -right-> alertCOE : No New OEM Model Necessary

@endum
