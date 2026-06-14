**Database — MVP**



\---



**0. Supporting Tables**



**import\_batches**



* id (UUID, PK)
* school\_id (UUID, FK -> schools.id)
* imported\_by (UUID, FK -> users.id)
* file\_name (String)
* status (Enum) — PENDING, PROCESSING, COMPLETED, FAILED
* row\_count (Integer, Nullable)
* error\_details (JSONB, Nullable)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



1\. **Global Identity Layer**



**users**



* id (UUID, PK)
* platform\_id (String) — (Partial Unique Index where deleted\_at IS NULL)
* is\_superadmin (Boolean, default False)
* email (String, Nullable) — (Partial Unique Index where deleted\_at IS NULL)
* username (String, Nullable) — (Partial Unique Index where deleted\_at IS NULL)
* password\_hash (String, Nullable) — (Nullable for future SSO)
* requires\_password\_change (Boolean, default True)
* is\_active (Boolean, default True)
* last\_signin\_at (Timestamp, Nullable)
* honorifics (String, Nullable)
* first\_name (String)
* middle\_name (String, Nullable)
* last\_name (String)
* dob (Date, Nullable)
* gender (Enum) — MALE, FEMALE, NON\_BINARY, PREFER\_NOT\_TO\_SAY
* pronouns (String, Nullable)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**2. Multi-Tenant Core \& Time Dimension**



**schools**



* id (UUID, PK)
* name (String)
* timezone (String, default 'UTC') — (e.g. 'Asia/Kolkata')
* is\_active (Boolean, default True) — (Billing kill switch)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**academic\_sessions**



* id (UUID, PK)
* school\_id (UUID, FK -> schools.id)
* name (String) — (e.g. "2024-2025")
* start\_date (Date)
* end\_date (Date)
* is\_active (Boolean, default False) — (Partial Unique Index on (school\_id) WHERE is\_active = true — enforces only one active session per school)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**user\_school\_roles**



* id (UUID, PK)
* user\_id (UUID, FK -> users.id)
* school\_id (UUID, FK -> schools.id)
* role (Enum) — ADMIN, TEACHER, STUDENT, PARENT (one row per role — fixes 1NF violation)
* is\_active (Boolean, default True)
* import\_batch\_id (UUID, FK -> import\_batches.id, Nullable) — (Links this role assignment to a specific Excel upload)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



(Unique Constraint: user\_id + school\_id + role)



\---



**3. Academic Infrastructure**



**year\_levels**



* id (UUID, PK)
* school\_id (UUID, FK -> schools.id)
* name (String)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



(Unique Constraint: school\_id + name WHERE deleted\_at IS NULL)



\---



**classes**



* id (UUID, PK)
* school\_id (UUID, FK -> schools.id)
* year\_level\_id (UUID, FK -> year\_levels.id)
* session\_id (UUID, FK -> academic\_sessions.id) — (Locks this class to a specific academic year)
* name (String)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



(Unique Constraint: school\_id + session\_id + name WHERE deleted\_at IS NULL)



\---



**teacher\_class\_associations**



* teacher\_id (UUID, FK -> users.id — Composite PK)
* class\_id (UUID, FK -> classes.id — Composite PK)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**student\_class\_associations**



* student\_id (UUID, FK -> users.id — Composite PK)
* class\_id (UUID, FK -> classes.id — Composite PK)
* enrollment\_status (Enum) — ACTIVE, DROPPED
* enrolled\_at (Timestamp)
* dropped\_at (Timestamp, Nullable)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**student\_parent\_associations**



* student\_id (UUID, FK -> users.id — Composite PK)
* parent\_id (UUID, FK -> users.id — Composite PK)
* is\_verified (Boolean, default False)
* verified\_at (Timestamp, Nullable)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**4. Security \& Auth**



**action\_tokens**



* id (UUID, PK)
* token (String, Unique) — (Minimum 32 bytes CSPRNG, e.g. secrets.token\_urlsafe(32))
* token\_type (Enum) — CREDENTIALS\_DOWNLOAD
* school\_id (UUID, FK -> schools.id, Nullable) (Nullable for future-proofing cases like password change token for superadmins)
* user\_id (UUID, FK -> users.id) — (Locks the download to the Admin who requested it)
* expires\_at (Timestamp)
* created\_ip\_address (String, nullable)
* created\_user\_agent (String, nullable)
* consumed\_at (Timestamp, Nullable) — (Null = unclaimed. Non-null = already used, reject any replay)
* consumed\_ip\_address (String, nullable)
* consumed\_user\_agent (String, nullable)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



(Table-Level Constraint):

CHECK (token\_type != 'CREDENTIALS\_DOWNLOAD' OR school\_id IS NOT NULL)



\---



**5. Legal \& Compliance**



**legal\_agreements**



* id (UUID, PK)
* agreement\_type (Enum) — CREDENTIAL\_DISTRIBUTION\_LIABILITY — (Unique Constraint with version)
* version (String) — (Unique Constraint with agreement\_type)
* content\_text (Text)
* is\_active (Boolean) — (Partial Unique Index on (agreement\_type) WHERE is\_active = true)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**user\_consents**



* id (UUID, PK)
* user\_id (UUID, FK -> users.id)
* legal\_agreement\_id (UUID, FK -> legal\_agreements.id)
* withdrawn\_at (Timestamp, Nullable) — (Consent withdrawal. Original acceptance record stays intact.)
* ip\_address (String)
* user\_agent (String)
* created\_by, updated\_by, deleted\_by (UUID, FK -> users.id, Nullable)
* created\_at, updated\_at, deleted\_at (Timestamp)



\---



**6. Audit**



**audit\_logs** — (Append-only. Never update or delete rows.)



* id (UUID, PK)
* actor\_id (UUID, FK -> users.id)
* school\_id (UUID, FK -> schools.id, Nullable) — (Nullable for system-level actions)
* action (Enum) — CREATED, UPDATED, DELETED, CLAIMED, EXPORTED
* entity\_type (Enum) — USER, SCHOOL, CLASS, YEAR\_LEVEL, ACADEMIC\_SESSION, ACTION\_TOKEN, LEGAL\_AGREEMENT, USER\_CONSENT
* entity\_id (UUID)
* details (JSONB)
* ip\_address (String)
* user\_agent (String)
* created\_at (Timestamp)

