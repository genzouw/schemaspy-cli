# schemaspy-cli
schemaspy bash interface

# Install

You only download this script, and place in a directory that In $PATH.

```bash:ex
cd ~/local/src
git clone git@github.com:genzouw/schemaspy-cli.git
export PATH="$PATH:~/local/src/schemaspy"
```

# Required

*Notice: Your OS is Redhat Family, Libraries will be installed automatically.*

* Java (JRE)
* JDBC Driver (for your database)


# Usage

```bash
schemaspy [-h DB_HOST] [-u DB_USER] [-p DB_PASSWORD] -t DB_TYPE -d DB_NAME
```

## DB_TYPE

You can use follow database type.

- psql
- mysql
- .. and so on.
   
You want to know detail, refer [this page](http://schemaspy.sourceforge.net/)
