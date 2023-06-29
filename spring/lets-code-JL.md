## Transaction:
  - Transactions are different for every single<br /> transaction type. [jta is different from jms, mongodb, jpa..]
  - PlatformTransactionManager is an interface that <br />describes the basic workflow of transaction.
    - it's a hierachy that describe a cross cutting <br />concerns, there are an implemetion for it in all spring data project.
    - when rollback happens, sequence val still the same
    https://stackoverflow.com/q/55294858/14840351
    
  