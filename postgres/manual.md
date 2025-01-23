- PostgreSQL is an object-relational database management system (ORDBMS) based on POSTGRES, Version 4.2, developed at the University of California at Berkeley Computer Science Department.
  - Support a large part of SQL standards:
    - complex queries
    - foreign keys
    - triggers
    - updatable views
    - transactional integrity
    - multiversion concurrency control
  - It can be extended by the user in many ways, for example, by adding new:
    - data types
    - functions
    - operators
    - aggregate functions
    - index methods
    - procedural languages
---
- 1.1
  - If you run it on a remote server, you need to set:
    - PGHOST : the name of the database server machine.
    - PGPORT : the TCP port the server is listening on.
---
- 1.2
  - PostgreSQL uses a client/server model. A PostgreSQL session consists of the following cooperating processes (programs): [As is typical of client/server applications, the client and the server can be on different hosts. In that case they communicate over a TCP/IP network connection.]
    - A server process, which manages the database files, accepts connections to the database from client applications, and performs database actions on behalf of the clients. The database server program is called postgres.
    - the user's client (frontend) application that wants to perform database operations. (a text-oriented tool, a graphical application, a web server that accesses the database to display web pages, or a specialized database maintenance tool.)
  - The PostgreSQL server handles multiple concurrent connections by creating a new process for each one("forks"). This allows each connection to be handled independently, without interference from the original server process. The original server process continues to run, waiting for new connections, while the client and associated server processes come and go.
  - /usr/local/pgsql/bin -> createdb mydb, dropdb
---
- 2.1
  - psql's -s option puts you in single step mode which pauses before sending each statement to the server.
    - \i basics.sql ->  \i FILE                execute commands from file
- 2.2
  - Whereas columns have a fixed order in each row, it is important to remember that SQL does not guarantee the order of the rows within the table in any way (although they can be explicitly sorted for display).
- 2.3 
  - Constants that are not simple numeric values usually must be surrounded by single quotes ('), as in the example. The date and point type.
  - The `COPY` command is a faster way to load large amounts of data from flat-text files into PostgreSQL. It is optimized for this task but less flexible than the `INSERT` command. The source file must be on the machine running the PostgreSQL server, not the client.
- 2.5
  - While SELECT * is useful for off-the-cuff queries, it is widely considered bad style in production code, since adding a column to the table would change the results.
  - In some database systems, including older versions of PostgreSQL, the implementation of DISTINCT automatically orders the rows and so ORDER BY is unnecessary. But this is not required by the SQL standard, and current PostgreSQL does not guarantee that DISTINCT causes the rows to be ordered.