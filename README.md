# Schemup

This is a fork of [Schemup](https://github.com/brendonh/schemup) for use with
postgres originally
written by [Brendonh](https://github.com/brendonh). You can view the detailed
original docs
[here](https://github.com/tmtxt/schemup/blob/master/README%20old.md). This is
just the short documentation about the basic usage and the utility binary that I
added.

# Installation

Create a file named **requirements.txt** with the content like this

```
psycopg2==2.5
pyyaml==3.10
git+git://github.com/tmtxt/schemup.git
```

Install it using **pip** (can be install through **virtualenv**)

```
$ pip install requirements.txt
```

# Schema Declaration

Create a folder named `migrations`. Add the schema definition in yaml format
there, one file for each table, for example

- **tbl_user.yaml**

```
---
table: tbl_user
from: null
to: version_1
sql: |
  CREATE TABLE "tbl_user" (
    "id" SERIAL NOT NULL PRIMARY KEY,
    "username" text NOT NULL UNIQUE,
  );

---
table: tbl_user
from: version_1
to: version_2
sql: |
  ALTER TABLE "icon_user" ADD COLUMN "gender" TEXT; -- male, female

---
table: tbl_user
from: version_2
to: version_3
sql: |
  ALTER TABLE "icon_user" ADD COLUMN "default_avatar" TEXT;
  ```

- **tbl_user_image.yaml**

```
---
table: tbl_user_image
from: null
to: version_1
depends:
  - ["tbl_user", version_1]
sql: |
  CREATE TABLE "tbl_user_image" (
      "id" SERIAL NOT NULL PRIMARY KEY,
      "image" TEXT NOT NULL,
      "user_id" INT REFERENCES "tbl_user"(id) ON DELETE CASCADE
  );

```

# Version declaration

Keep the map in a json file.

**versions.json**

```json
    {
        "tbl_user": "version_3",
        "tbl_user_image": "version_1"
    }
```

# Database declaration

Create a file name `db.json` at the same folder with the previous `migrations`
folder and `versions.json` file

```
{
	"database": "vagrant",
	"user": "vagrant",
	"password": "vagrant",
	"host": "localhost",
	"port": 5432
}
```

# Migration

Run `schemup` command (will be created when you install **schemup**) in the
directory that contains all the above files

```
$ schemup commit
Importing migrations/tbl_user.yaml
Importing migrations/tbl_user_image.yaml
```
