- A reliable distributed system is composed of reliable applications.

# properties of a reliable application
- Idempotent
    - it help us to tolerate unreliable networks. 
        - if the user retry, the idempotent application will not duplicate the effect it receive a duplicate message.
- Immutability
    -  
- Location Independent
    - the application can be deployed anywhere.
- Versioning
    - things change over time, and a well-versioned application will tolerate those changes over time.