###When PostgreSQL is installed on at windows server, there are some tricks you can use.###

In order for Apache to connect to PostgreSQL, Apache needs info about IP, Port, Database, User and password.
On top of that, one can also supply info about Client Encoding, and set some Attributes.

I do this by having these environment variables:

PGCLIENTENCODING=UTF8
PGHOST=<PostgreSQL ip>
PGPASSFILE=C:\Users\<WindowsUserRunningApacheService>\AppData\Roaming\postgresql\pgpass.conf
PGPORT=<PostgreSQL Port>
PGUSER=<PostgreSQL user>

The file pgpass.conf has this line:

´<ip>:<port>:*:<PostgreSQL user>:<password>`

My Apache is setup to use a dedicated user on my server.
