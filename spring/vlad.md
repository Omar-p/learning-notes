# project structure

- mvn clean test-compile
  - don't run mvn install because some of tests designed to be run in minutes


- init method :
    - if u override init method, u should call super.init() method.

- afterInit method :
    - run after init method


- if u extend AbstractTest:
    - the test will run aganist in-memory hypersonic database

- to override database:
    -  protected Database getDatabase() {
    -    return Database.MYSQL;
    -  }
    <hr>
<hr>
<hr>


- why jdbc logging is very important when working with hibernate&jpa
    - what configuration does hibernate offer to customize logging
    - why jdbc data source or driver proxy is much more flexible alternative to hibernate logging.