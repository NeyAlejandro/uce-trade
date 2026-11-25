# Keycloak Realm Configuration (`infra/keycloak`)

This directory stores the Keycloak realm export and role mapping rules for **UCE Trade** (`FR-01` to `FR-03`, `NFR-01`):

- **Realm:** `uce-trade`
- **Roles:**
  - `UCE_ADMIN`: Platform administrators and moderators.
  - `UCE_STUDENT`: Automatically assigned to accounts registered with an `@uce.edu.ec` institutional email (`FR-02`).
  - `UCE_CLIENT`: Assigned to accounts registered with external email domains such as Gmail or Outlook (`FR-03`).
