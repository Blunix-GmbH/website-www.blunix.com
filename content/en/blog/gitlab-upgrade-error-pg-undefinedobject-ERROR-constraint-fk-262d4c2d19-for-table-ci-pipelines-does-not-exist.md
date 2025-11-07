---
title: 'Howto fix Gitlab Upgrade Error: PG::UndefinedObject: ERROR: constraint "fk_262d4c2d19" for table "ci_pipelines" does not exist'
description: 'Howto fix Gitlab Upgrade Error: PG::UndefinedObject: ERROR:  constraint "fk_262d4c2d19" for table "ci_pipelines" does not exist on Debian Linux'
date: 2024-01-22
image: "/images/blog/gitlab-logo.webp"
image_alt: 'Gitlab Upgrade Error PG::UndefinedObject: ERROR: constraint "fk_262d4c2d19" for table "ci_pipelines" does not exist'
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

While upgrading from the apt installed omnibus gitlab Version 16.6.0-ce.0 on Debian 12, I encountered the following error message:

```plaintext
PG::UndefinedObject: ERROR:  constraint "fk_262d4c2d19" for table "ci_pipelines" does not exist
```

The following SQL query displays all present postgresql constraints:

```plaintext
root@gitlab.server ~ # gitlab-psql
gitlabhq_production=# SELECT con.*
       FROM pg_catalog.pg_constraint con
            INNER JOIN pg_catalog.pg_class rel
                       ON rel.oid = con.conrelid
            INNER JOIN pg_catalog.pg_namespace nsp
                       ON nsp.oid = connamespace
       WHERE nsp.nspname = 'public'
             AND rel.relname = 'ci_pipelines';


  oid  |      conname      | connamespace | contype | condeferrable | condeferred | convalidated | conrelid | contypid | conindid | conparentid | confrelid | confupdtype | confdeltype | confmatchtype | conislocal | coninhcount | connoinheirt | conkey | confkey | conpfeqop | conppeqop | conffeqop | conexclop | conbin
-------+-------------------+--------------+---------+---------------+-------------+--------------+----------+----------+----------+-------------+-----------+-------------+-------------+---------------+------------+-------------+-----------
---+--------+---------+-----------+-----------+-----------+-----------+--------
 80430 | fk_190998ef09     |         2200 | f       | f             | f           | t            |    66450 |        0 |    75938 |           0 |     67466 | a           | n           | s             | t          |           0 | t           | {27}   | {1}     | {410}     | {410}     | {410}     |           |

 75711 | ci_pipelines_pkey |         2200 | p       | f             | f           | t            |    66450 |        0 |    75710 |           0 |         0 |             |             |               | t          |           0 | t           | {1}    |         |           |           |           |           |

 80714 | fk_3d34ab2e06     |         2200 | f       | f             | f           | t            |    66450 |        0 |    75704 |           0 |     66430 | a           | n           | s             | t          |           0 | t            | {18}   | {1}     | {96}      | {96}      | {96}      |           |

 80977 | fk_67e4288f3a     |         2200 | f       | f             | f           | t            |    66450 |        0 |    75710 |           0 |     66450 | a           | n           | s             | t          |           0 | t            | {32}   | {1}     | {15}      | {96}      | {410}     |           |

 81675 | fk_c262d728d4     |         2200 | f       | f             | f           | t            |    66450 |        0 |    75710 |           0 |     66450 | a           | n           | s             | t          |           0 | t            | {17}   | {1}     | {96}      | {96}      | {96}      |           |

 81870 | fk_d80e161c54     |         2200 | f       | f             | f           | t            |    66450 |        0 |    75718 |           0 |     66491 | a           | n           | s             | t          |           0 | t           | {28}   | {1}     | {410}     | {410}     | {410}     |           |

(6 rows)
```
