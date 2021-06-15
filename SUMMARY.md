# Table of contents

* [Welcome to the StackState Docs!](README.md)

## 🚀 Setup

* [Introduction to StackState](use/introduction-to-stackstate/README.md)
  * [Getting Started](use/introduction-to-stackstate/getting_started.md)
  * [The 4T data model](use/introduction-to-stackstate/4t_data_model.md)
  * [Components and Relations](use/introduction-to-stackstate/components_and_relations.md)
  * [Perspectives](use/introduction-to-stackstate/perspectives.md)
  * [Anomaly detection](use/introduction-to-stackstate/anomaly-detection.md)
  * [Layers, Domains and Environments](use/introduction-to-stackstate/layers_domains_and_environments.md)
  * [Data protection features](/use/introduction-to-stackstate/data-protection/README.md)
    * [Data flow architecture](/use/introduction-to-stackstate/data-protection/data-flow-architecture.md)
    * [Data per component](/use/introduction-to-stackstate/data-protection/data-per-component.md)
    * [SaaS](/use/introduction-to-stackstate/data-protection/saas.md)
    * [Self-hosted StackState](/use/introduction-to-stackstate/data-protection/self-hosted.md)
  * [StackState architecture](use/introduction-to-stackstate/stackstate_architecture.md)
* [Release notes](setup/upgrade-stackstate/sts-release-notes.md)
* [StackState CLI](setup/installation/cli-install.md)

## 👤 Use

* [Explore mode](use/explore_mode.md)
* [View filters](use/view_filters.md)
* [Views](use/views.md)
* [Perspectives](use/perspectives/README.md)
  * [Topology Perspective](use/perspectives/topology-perspective.md)
  * [Events Perspective](use/perspectives/events_perspective.md)
  * [Traces Perspective](use/perspectives/traces-perspective.md)
  * [Telemetry Perspective](use/perspectives/telemetry-perspective.md)
  * [Browse telemetry](use/perspectives/browse-telemetry.md)
* [Health state and event notifications](use/health-state-and-event-notifications/README.md)
  * [Health state in StackState](/use/health-state-and-event-notifications/health-state-in-stackstate.md)
  * [Add a health check](use/health-state-and-event-notifications/add-a-health-check.md)
  * [Add a telemetry stream](use/health-state-and-event-notifications/add-telemetry-to-element.md)
  * [Configure the view health](use/health-state-and-event-notifications/configure-view-health.md)
  * [Send event notifications](use/health-state-and-event-notifications/send-event-notifications.md)
  * [Anomaly health checks](use/health-state-and-event-notifications/anomaly-health-checks.md)
  * [Anomaly detection with baselines \(Deprecated\)](use/health-state-and-event-notifications/anomaly-detection-with-baselines.md)
* [Problems](use/problems/README.md)
  * [What is a problem?](use/problems/problems.md)
  * [Investigate a problem](use/problems/problem_investigation.md)
  * [Problem notifications](use/problems/problem_notifications.md)
* [Analytics](use/analytics.md)
* [Glossary](use/glossary.md)

## 🧩StackPacks

* [About StackPacks](stackpacks/about-stackpacks.md)
* [StackState Agent](stackpacks/integrations/agent.md)
* [AWS](stackpacks/integrations/aws.md)
* [AWS X-ray](stackpacks/integrations/aws-x-ray.md)
* [Kubernetes](stackpacks/integrations/kubernetes.md)
* [Openshift](stackpacks/integrations/openshift.md)
* [Slack](stackpacks/integrations/slack.md)
* [Add on: Autonomous Anomaly Detector](stackpacks/add-ons/aad.md)
* [Add on: Health Forecast](stackpacks/add-ons/health-forecast.md)


## 🔧 Configure

* [Tags](configure/topology/tagging.md)
* [Checks and streams](configure/telemetry/checks_and_streams.md)
* [Set up traces](configure/traces/how_to_setup_traces.md)  
* [Health](configure/health/README.md)
  * [Health synchronization](configure/health/health-synchronization.md)
  * [Send health data over HTTP](configure/health/send-health-data.md)
  * [Debug health synchronization](configure/health/debug-health-sync.md)
* [Security](configure/security/README.md)
  * [Authentication](configure/security/authentication/README.md)
    * [Authentication options](configure/security/authentication/authentication_options.md)
    * [File based](configure/security/authentication/file.md)
    * [LDAP](configure/security/authentication/ldap.md)
    * [Open ID Connect \(OIDC\)](configure/security/authentication/oidc.md)
    * [KeyCloak](configure/security/authentication/keycloak.md)
  * [RBAC](configure/security/rbac/README.md)
    * [Role-based Access Control](configure/security/rbac/role_based_access_control.md)
    * [Permissions](configure/security/rbac/rbac_permissions.md)
    * [Roles](configure/security/rbac/rbac_roles.md)
    * [Scopes](configure/security/rbac/rbac_scopes.md)
    * [Subjects](configure/security/rbac/rbac_subjects.md)
  * [Self-signed certificates](configure/security/self-signed-cert.md)
  * [Secrets management](configure/security/secrets_management.md)
  * [Self-signed certificates](configure/security/self-signed-cert-1.md)
  * [Set up a security backend for Linux](configure/security/set_up_a_security_backend_for_linux.md)
  * [Set up a security backend for Windows](configure/security/set_up_a_security_backend_for_windows.md)

## 📖 Reference

* [StackState CLI reference](develop/reference/cli_reference.md)
* [StackState Query Language \(STQL\) reference](develop/reference/stql_reference.md)
