# Chat so far

**User:** Postgres or MongoDB for the portal?

**Assistant:** PostgreSQL on a managed host. Rule 1: both backend developers, Sam and Priya, have years of Postgres; nobody on the team has used MongoDB in production. Rule 2: managed hosting. The portal's data (accounts, orders, invoices) is relational and fits Postgres well.

**User:** OK, go with Postgres.
