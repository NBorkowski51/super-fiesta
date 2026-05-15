# Integrity Product Knowledge

## Purpose

This file is a practical product map of the related codebases in this workspace:

- Integrity
- Dashboard
- Asset Models

It is intended for engineers, support, and onboarding teams who need to quickly understand what the Integrity product includes, how major components connect, and what each subsystem is capable of.

This is an engineering knowledge document, not a formal product contract. Capabilities are based on repository structure and verified code/configuration touchpoints.

---

## Product Context: Three Related Folders

### Integrity (core product)
Integrity is the primary product platform. It owns:

- data collection and ingestion
- asset normalization and storage
- cybersecurity and operational workflows
- inventory, vulnerability, baselines, topology, and reporting
- user/security administration and integrations

### Dashboard (NOCDash)
Dashboard is a related but separately built web application used with Integrity for summary and analytics views. In practice, Integrity links users into Dashboard pages and Dashboard consumes Integrity-side data/services.

### Asset Models
Asset Models provide the normalization layer that makes data from many OT/ICS systems comparable. They define object types, properties, references, and model-specific rules used by Integrity and surfaced in Dashboard.

### Core Technologies
- **.NET Framework**: Primary development platform
- **VB.NET & C#**: Mixed language codebase
- **SQL Server**: Primary database with full-text search
- **ASP.NET Web Forms**: Web presentation layer
- **WCF/Named Pipes**: Inter-service communication

### Key Dependencies
- **IIS**: Web application hosting
- **Windows Services**: Background processing
- **Active Directory**: Authentication integration

---

## How The Three Systems Work Together

1. Collectors ingest source data from OT/IT systems.
2. Asset Models normalize source data into a common structure.
3. Integrity stores and processes normalized data for inventory, vulnerabilities, workflows, baselines, and topology.
4. Dashboard reads the same ecosystem (via Integrity/DAL/AssetModeler dependencies and local service bindings) and presents summary views and KPI widgets.
5. Integrity pages include routes/links for dashboard experiences (embedded or linked navigation patterns).

---

## Verified Integration Evidence (Integrity <-> Dashboard)

### Dashboard URL configuration exists in Integrity model root
In AssetModeler root configuration, Integrity stores and derives dashboard endpoints:

- `DashboardURL` and `DashboardRoot` exist and are persisted/read from configuration.
- Comments indicate expected pattern like `http://[domain]/dashboard/dashboard.aspx`.

Relevant code:

- `Integrity/AssetModeler/AssetModelerRoot.vb`

### Integrity constructs and uses Dashboard links
Integrity default page code builds Dashboard URLs and applies fallback behavior:

- `GetDashboardLink()` appends `dashboard.aspx`
- automation product query pattern is appended in one branch
- fallback route exists to cyber dashboard page when no external URL configured

Relevant code:

- `Integrity/Integrity/Default.aspx.vb`

### Integrity UI includes dashboard-oriented sections
Cyber default navigation maps include keys for dashboard-centric summary areas such as:

- `PerformanceSummary`
- `InventoryDashboard_Group`
- `VMSummaryDashboard_Group`

Relevant code:

- `Integrity/Integrity/Cyber/default.aspx`

### Dashboard is wired to Integrity data/service stack
Dashboard web app references core Integrity-side assemblies and DAL artifacts:

- AssetModeler
- DAL / DALCs / DAL_Interfaces
- Integrity.Core
- Integrity.Infrastructure

Relevant project/config:

- `Dashboard/NOCDash/NOCDash.csproj`

### Dashboard client uses named pipes for local DB/service connectivity
Dashboard web config declares netNamedPipe client endpoint:

- endpoint address `net.pipe://localhost/`
- contract `PAS.pasIntegrityDAL.DBConnect.IDBConnect`

Relevant config:

- `Dashboard/NOCDash/Web.config`

### Dashboard can enumerate Integrity data owners and asset models
Dashboard configuration page code loads data owners and asset models via `AssetModelerRoot`.

Relevant code:

- `Dashboard/NOCDash/cfgDataSource.ascx.cs`

---

## User-Visible Dashboard Usage In Integrity Context

Based on code and product structure, Dashboard is used from Integrity for summary/KPI experiences, including scenarios such as:

- main dashboard landing experience
- inventory summary views
- vulnerability summary views
- event/log analytics style summaries and monitoring windows
- widget-driven drill-in where dashboard interactions route users into Integrity pages

This aligns with Integrity navigation hooks and Dashboard menu/KPI configuration patterns.

---

## Integrity Component Capabilities (By Subsystem)

## 1) Core Platform and Shared Libraries

Primary capability: shared business types, infrastructure concerns, persistence contracts, and common services.

Major components:

- `Integrity/Integrity.Core`
- `Integrity/Integrity.Infrastructure`
- `Integrity/Integrity.SharedKernel`
- `Integrity/DAL`
- `Integrity/DAL_Interfaces`
- `Integrity/DALCs`

What they enable:

- reusable models and domain primitives
- data access patterns and storage contracts
- shared utility and infrastructure logic
- consistent behavior across web app, services, and utilities

## 2) Data Collection and Ingestion

Primary capability: acquire data from diverse sources and bring into Integrity processing pipeline.

Major components:

- `Integrity/IntegrityDataCollector`
- `Integrity/DataCollector`
- `Integrity/WMIDC`
- `Integrity/WindowsEventAnalyticsCollector`
- `Integrity/WindowsEventAnalyticsParser`
- `Integrity/WindowsEventAnalyticsMessenger`

What they enable:

- scheduled/continuous ingestion
- WMI integrations
- event analytics collection pipeline
- source-specific adapters and import orchestration

## Deployment Patterns

### Typical Installation Components
- **Web Server**: IIS hosting Integrity web app and API
- **Application Server**: Windows services for data collection and processing  
- **Database Server**: SQL Server with Integrity schema
- **Dashboard Server**: Can be co-located or separate NOCDash instance


## 3) Asset Modeling and Extensibility

Primary capability: normalize heterogeneous system data and allow product extension.

Major components:

- `Integrity/AssetModeler`
- `Integrity/AssetModelBuilder`
- `Integrity/AssetModelDevelopmentKit2`
- `Integrity/AssetModelCode`
- `Integrity/CustomProperties`

What they enable:

- creation and maintenance of model schemas
- custom properties and viewer behavior
- validation/defect checks based on model definitions
- integration of new vendor/system data formats

## 4) Core Business Domains

Primary capability: operational and cybersecurity workflows on normalized asset data.

Major components:

- `Integrity/Inventory`
- `Integrity/InventoryItemsWizard`
- `Integrity/VulnerabilityManagement`
- `Integrity/Baselines`
- `Integrity/Recon`
- `Integrity/HierarchyIndicator`
- `Integrity/Drawing`

What they enable:

- inventory discovery and normalization
- vulnerability detection and workflow handling
- baseline creation and comparison over time
- topology and reconciliation views
- hierarchy/group-oriented operational analysis

## 5) Workflow, Notification, and Case Management

Primary capability: coordinate remediation, approvals, assignment, and audit trail through defined process states.

Major components:

- `Integrity/Workflow`
- `Integrity/Notifications`
- `Integrity/Email`
- `Integrity/Alerts`

What they enable:

- case lifecycle management
- state transitions and ownership changes
- email/alert triggers and templated notifications
- operational governance of remediation activities

## 6) Web/API Presentation Layer

Primary capability: user and programmatic access to Integrity data and actions.

Major components:

- `Integrity/Integrity` (main web app)
- `Integrity/IntegrityWebApi`
- `Integrity/WebViewers`
- `Integrity/Reports`
- `Integrity/Search`

What they enable:

- primary user interface for asset and process operations
- API-driven integrations and automation
- reporting and search experiences
- model-aware viewing of data

## 7) Security, Administration, and Operations

Primary capability: identity/access control, service operation, and environment maintenance.

Major components:

- `Integrity/AppAuthentication`
- `Integrity/Permissions`
- `Integrity/Policy`
- `Integrity/IntegrityADUserTool`
- `Integrity/IntegrityServiceManager`
- `Integrity/ConfigurationManager`
- `Integrity/AdminUtility`

What they enable:

- role and policy based access
- AD/user synchronization
- operational service management
- centralized configuration management

## 8) Upgrade, Deployment, and Packaging

Primary capability: install, upgrade, and package Integrity ecosystem components.

Major components:

- `Integrity/DBUpgradeUtility`
- `Integrity/IntegrityUpgrade`
- `Integrity/UpgradeUtility`
- `Integrity/Installation`
- `Integrity/ICEMaker`
- `Integrity/ICEMakerSetup`
- `Integrity/Integrity2.0_deploy`

What they enable:

- database migration and product upgrade flows
- release packaging and install media creation
- deploy-time orchestration of multi-component systems

---

## Dashboard Capability Summary

Main dashboard components:

- `Dashboard/NOCDash` (web app)
- `Dashboard/DashboardBuilder` (configuration/builder utility)
- `Dashboard/NOCDashCore`
- `Dashboard/ExtractWidgets`
- `Dashboard/WindowsEventsTester` (support/test utility)

What Dashboard is capable of in this ecosystem:

- render KPI and summary pages for product domains
- provide menu-driven product variants (Cyber Integrity, Automation Integrity, OpsClarity, PSS visibility patterns)
- display inventory and vulnerability summary style widgets
- support event log/analytics monitoring views in configuration area
- consume Integrity-side data constructs through shared dependencies and local service endpoint configuration

---

## Asset Models Capability Summary

Root location:

- `Asset Models/Asset Models`

Observed scope:

- 50+ vendor/system model folders (Siemens, Honeywell, GE, ABB, Emerson, Rockwell, Yokogawa, Schneider, etc.)
- PAS-specific model sets (Baselines, Policies, Recon, Topology, Workflows, Cyber Integrity)
- integration/third-party model families (for specialized ecosystems)

What Asset Models are capable of in this ecosystem:

- normalize multi-vendor system inventories into a common data language
- map source-specific fields into Integrity-consumable objects/properties
- support model-driven behaviors in inventory, vulnerability, baseline, and dashboard views
- provide extension path for new sources and plant-specific schemas

---

## Example End-to-End Capability Scenarios

### Inventory summary workflow

1. Collectors ingest system/software data.
2. Asset Models normalize and classify entities.
3. Integrity inventory modules process and store normalized results.
4. Dashboard exposes inventory summary KPIs/widgets for quick operational view.

### Vulnerability summary workflow

1. Asset and software context is collected and normalized.
2. Vulnerability module correlates with vulnerability intelligence/process rules.
3. Workflows and notifications manage investigation/remediation.
4. Dashboard displays vulnerability summary indicators and links/drill-ins.

### Event analytics summary workflow

1. Windows event analytics collectors/parsers ingest and process event streams.
2. Integrity stores/relates events with assets and workflows.
3. Dashboard configuration/monitoring surfaces event log status and activity summaries.

### Main dashboard access from Integrity

1. Integrity computes dashboard URL from configured `DashboardURL` or fallback routes.
2. User navigation points route to dashboard contexts.
3. Dashboard pages provide cross-domain summary widgets and navigation.

---

## Repository Orientation Guide (Fast Onboarding)

Recommended reading order:

1. `Integrity/Product Documentation/ICSIntegrityHelp`
2. `Integrity/Product Documentation/AMDK-Help`
3. `Integrity/PRODUCT_KNOWLEDGE.md` (this file)
4. `Integrity/Integrity` and `Integrity/IntegrityWebApi`
5. `Integrity/DataCollector`, `Integrity/IntegrityDataCollector`, and connector projects
6. `Dashboard/NOCDash` and `Dashboard/NOCDashCore`
7. `Asset Models/Asset Models`

---

## Known Unknowns (To Refine)

These points need deeper code-level verification if required for architecture sign-off:

- exact startup dependency graph among all Windows services
- exact set of active vs legacy projects in the large Integrity solution
- authoritative source for Dashboard embedding mode per page (iframe/navigation/hybrid) across all product variants
- exact API contract inventory currently used by Dashboard vs legacy DAL paths

---

## Maintenance Notes

When updating this file:

- prefer evidence-backed statements tied to concrete project/config files
- mark inferred behavior explicitly
- keep capability descriptions product-level and implementation-neutral unless code evidence is direct
- update this map when major service boundaries or integration patterns change
