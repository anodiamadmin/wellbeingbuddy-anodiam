**WellBeingBuddy Decisions:**



1. **Audit Trail:**
* Including **created\_at**, **updated\_at**, **created\_by**, **updated\_by** tables for audit
* Implementing **Soft Delete: deleted\_at** (NOT NULL) and **deleted\_by** tables
* Not including **shadow tables** for **user History** in MVP
* Letting Cloud Provider **encrypted backup** take care of **User History** with a **30-day retention** policy



**2. Security Tradeoffs:**

* Using **HS256** algorithm for encryptions, good for monolithic architecture.
* When WellbeingBuddy moves to **microservices**, need to switch to **RS256**.



**3. API Functionality:**

* No provision of update/upsert for mass/selective promotion (or held back) at the end of academic year for students in the MVP (requires an unique **identifier**, preferably from school. E.g.- Registration ID)
* A database table named **academic\_session** is provisioned which partially enables the session management, yearly updating logic is not in MVP.



**4. Parents' KBA:**

* Parents won't need to type student username every time they open WellBeingBuddy web app. Example- Opening the close tab (within the span of an active, authenticated session)
* Parents need to type student username only if they are logged out, trying to sign back in.



**5. Core Features:**

* Provisions for **Account Switching** feature (For users who got more than 1 role) has been **built** but not kept in the web app UI for Sprint 1 (Mar 9, 2026 - Apr 2, 2026).
* **Student** users' existing wellbeingbuddy account cannot be migrated when they leave school and join another as they don't have an email.
* **Admin** users are currently locked to 1 school - 1 email approach.

