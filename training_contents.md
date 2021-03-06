KFS TRAINING CONTENT - OCTOBER 2018
=======================

- What to expect here
  - You will NOT know everything
  - Better holistic understanding
  - Better knowledge base about how to find what you need
  - It's gargantuan, built by multiple teams, spanning 15 years
- Overview & Structure
  - Three things are hard in KFS
    - The business domain
    - How to find the code where the bug is
    - How to repro the bug in local environment
  - 2 systems (kfs & rice)
    - Plus some rice libs included in kfs
      - (open PersistableBusinessObjectBase) and show libs
    - Mostly you can ignore the fact that Rice is remote, except:
      - (show both URLs)
      - Different URL to address rice screens directly
      - Be aware when your rice pages are IFrame'd in the portal (show)
      - Doc Search, Action List, Perms, Roles, DocTypes
  - 4 code projects (ucd-kfs, kfs-baseline, ucd-rice, rice-baseline)
    - Don't put kfs-baseline and rice-baseline in Eclipse
    - grep for non-java searches
      - `grep -R -i --include "*.xml" "yourSearchTerm" .`
    - How to get eclipse project errors "unstuck"
  - The overlay
    - Why is there org.kuali code in ucd-kfs?
    - Examples:
  - The Database(s)
    - 4 schemas (plus others)
    - Naming conventions
    - Snapshots FTW
    - DB schema differences between Local and Deployed environments
    - What will change in KFS7/KFS++/KFS-TheFuture?
- Finding Things in KFS, part 1
  - So many conventions!  learn them use them
  - Expectation Setting:  If you don't already know this, you won't have it all memorized after this training
  - The front-end
    - Portal files (portal.jsp and children)
    - JSP vs Tag files (InternalBilling.jsp and child tag files)
      - Pain points of the UI files
        - excessive re-use of tag files causing too much logic (camsItems.tag)
        - too much logic here, which could be done in the form (like KualiForm.currentTabIndex)
    - JavaScript
      - DWR
        - Think of it as a super early form of GraphQL
      - jQuery
      - React on KFS7
  - The Code
    - Struts action & form classes (InternalBilling, PurchaseOrder)
    - Business object (Account)
    - Documents (InternalBillingDocument, PurchaseOrder)
    - Maintainables (AccountMaintainable)
    - Lookup helpers
    - Services
      - SpringContext
      - ServiceLocators
      - When to use SpringContext.getBean vs ServiceLocators
        - Local services use SpringContext, Rice services use Locators
    - Batch Steps
    - Integration points
    - GlobalVariables
      - ThreadLocal
      - Only in batch jobs and API endpoints do you muck with the UserSession
      - You only will run into this with Validations and batch jobs, mostly
  - The XML
    - OJB
      - baseline: ojb-<module>.xml
      - UCD overrides: ojb-<module>-override.xml
      - UCD New: ojb-<module>-uc.xml
    - Spring
      - baseline: spring-<module>.xml (also the module declaration)
      - UCD overrides: spring-<module>-uc.xml
      - UCD TP specific: spring-<module>-uc-tp-only.xml
      - UCD Batch specific: spring-<module>-uc-batch.xml
    - DataDictionary
      - Quick overview of the datadictionary
      - Remember, this is Spring, so if you want to see what options are available, just find the underlying java object!
      - Documents
        - baseline: InternalBillingDocument.xml (from kfs-baseline)
        - UCD overrrides:  InternalBillingDocument.xml (from ucd-kfs)
      - BusinessObjects
        - baseline: Account.xml (from kfs-baseline)
        - UCD overrides Account.xml (from ucd-kfs)
      - MaintenanceDocuments
        - baseline:  AccountMaintenanceDocument.xml (kfs-baseline)
        - UCD override:  AccountMaintenanceDocument.xml (ucd-kfs)
      - Validations
        - Documents
          - baseline:  InternalBillingValidation.xml (from kfs-baseline)
          - UCD overrides:  InternalBillingValidation.xml (from ucd-kfs)
        - MaintenanceDocuments
          - UcKualiAccountRule.java
- Finding Things in KFS, part 2
  - How to find the code from a URL
    - First, bust the portal frame (channelUrl)
    - Lookup
    - Inquiry
    - Maintenance Document
    - Document
  - How to find what user I need to backdoor as?
    - Ie, permissions, roles, groups
    - PDP links
    - JournalVoucher
  - How to find the stacktrace in the logs?
    - Alternative to the raw logs is kibana
      - `https://afs-elk.ucdavis.edu/app/kibana`
  - How to find the feed files for batch job issues
- DBAccess/OJB
  - OJB is a fairly simple ORM
  - References
  - Collections
  - Proxies
  - conversions
  - auto-retrieve, auto-update, auto-delete
  - Consider using raw SQL via Jdbc when needed!
- Struts
  - Intro to Struts
    - Struts isn't scary, it's just old
    - It's a typical early-gen server-side MVC
  - Struts classes are NOT Spring Managed!
    - You have to use SpringContext.getBean() and ServiceLocators
  - struts-config xml
  - Actions / methodToCall
  - Forms / document + other fields
    - Buttons / getExtraButtons
    - KualiForm.getExtraButtons()
      - UcPurchseOrderForm.getExtraButtons()
    - "regular" document buttons added automatically
  - Important hooks
    - populate()
    - refresh()
    - execute()
      - Runs before the methodToCall method
    - loadDocument()
    -
  - Document Actions
    - Route, Save, Cancel, Approve, etc
  - Permissions (DocumentActions & EditModes)
    - This isn't really struts, but it is all about the UI
    - DocumentAuthorizers
      - Document permission based on the person
      - Look at both DocumentAuthorizer interfaces
    - PresentationControllers
      - Document permissions based on the document state
      - UcPurchaseOrderDocumentPresentationController
      - Look at both DocumentPresentationController interfaces
- KIM Roles, Groups, Permissions
  - Difference between Roles and Groups
  - Qualified Roles
  - Qualified Permission
  - Show how document attributes and role qualifiers permission qualifiers
- DataDictionary
  - BusinessObjects
  - Documents
  - DocumentValidations
  - Maintenance Documents
- Document Workflow
  - WARNING: This will not be intuitive probably, this may look crazy and overcomplex
    - You will probably not understand this until you've worked through an example or two
  - Workflow XML (including inheritance)
    - The "role" nodes do not mean KIM Roles
    - routePaths vs routeNodes in the xml
  - Responsibilities
    - Responsibilities are a link between route-nodes and KIM roles
  - Putting it all together
    - ucd-kfs good reference
      - ProcurementCardDocument.xml
        - shows local defined stuff
    - Workflow XML for InternalBilling document
    - `<role name="Account" nextNode="AccountingOrganizationHierarchy" />`
    - Find the Responsibility for this
      - It's hooked to the parent doc type, so a pain to find
      - KFS-SYS, Review KFS Account
    - role="Account" is the "routeNodeName" from the KIM Responsibility
    - Fiscal Officer is role (derived) assigned to this responsibility
      - AccountDerivedRoleTypeServiceImpl.java
    - How does KIM/Responsibility get the Data to determine which Fiscal Officer needs to approve it?
      - InternalBillingDocument.xml, bean: InternalBillingDocument-workflowAttributes
      - FinancialProcessingAccountingDocumentDefinitions.xml, bean: FinancialProcessingDocument-workflowAttributes
        - `<entry key="Account" value-ref="RoutingType-AccountingDocument-Account"/>`
        - This is the common set of route nodes for AccountingLine documents (any given doc may have more)
      - `FinancialSystemDataDictionaryBaseTypes.xml`, bean: `RoutingType-AccountingDocument-Account`
        - routingAttributes describes the named attributes needed for FiscalOfficer routing
        - documentValuePathGroups describes the location on the document to get the values
      - routingAttributes bean: RoutingAttribute-accountNumber
        - `grep -R -i --include "*.xml" "bean id=\"RoutingAttribute-accountNumber" . `
        - no luck
        - `grep -R -i --include "*.xml" "alias=\"RoutingAttribute-accountNumber" .`
        - wow
        - `kfs-baseline: kfs-core/src/main/resources/org/kuali/kfs/sys/document/datadictionary/FinancialSystemDataDictionaryBaseTypes.xml`
        - `<alias name="RoutingAttribute-Account" alias="RoutingAttribute-accountNumber"/>`
        - `kfs-baseline: kfs-core/src/main/resources/org/kuali/kfs/sys/document/datadictionary/KfsBaseWorkflowPropertyBeans.xml`
      - Back to documentValuePathGroups: `RoutingType-AccountingDocument-Account` in `FinancialSystemDataDictionaryBaseTypes.xml`
        -
- Asynchronous Actions & Transaction Boundaries
  - So important it gets its own section
  - Document routing and most actions are synch
    - Immediately return, even before routing is done
  - What this means for Transaction Boundaries
  - Batch Jobs
    - Wrapped Step (giant wrapping transaction)
    - (show examples)
- Validations
  - Transactional document validations
    - Accounting Line validations
    - Validation events
  - Maintenance document validations
- Miscellaneous
  - StringUtils.isBlank/isNotBlank
  - StringUtils.equals/equalsIgnoreCase
  - ObjectUtils.isNull
  - KualiDecimal vs. BigDecimal
  - BigDecimal.signum
  - BigDecimal.compareTo
  - Lazy getters for consuming service from Struts or non-spring-objects
  - Why you should never use a floating-point datatype for money
- Real World Examples
  - Jiras
    - KFS-20251: Removing VOID button for a certain type of PO
    - KFS-20191: Issue with international addresses
    - KFS-19728: negative tax issues
    - KFS-19802: FYI to requesters when doc is Disapproved
    - KFS-19730: Restricting a field to a new permission
    - KFS-19731: Refreshing vendor address on Copy
    - KFS-18610: Removing a link to doc type on portal
    - KFS-18426: Adding a new validation
    - KFS-18377: Adding new rules to validation with new messages/properties
    - KFS-19779: Crazy long validation steps
    - KFSCR-665: Waiting for an async doc routing to get to final, with timeout
      - KfsPurapRequisitionServiceImpl line 208 (waitForFinal())
    - KFS-19803: proposed new validations
  - Enhancements
    - Mask a field on a BO
    - Add a new validation on a document
    - Changing workflow routing, adding a new split node
      - https://afs-dev.ucdavis.edu/jira/browse/KFS-20186
  - Bugs
    - Async/Transactional Issue
      - https://afs-dev.ucdavis.edu/jira/browse/KFS-20250
- Unit Tests
  - Mocks
  - PowerMocks
    - (KFS-20186)
  - If you're not sure how something works, write a unit test!
- Class Exercises
  - Mask a field on Account ... use an existing permission
  - New validation on IB that requires a specific account to be used
  - Populate sub-account from account on IB on Save
  - New routing node on IB
