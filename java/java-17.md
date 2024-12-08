### Generic
- Unlike most of java's typing, the generic aspect of type does not necessarily persist in to the runtime.(Type Erasure)
  - This is necessary to maintain backward compatibility with pre-generic code.
    - it also keeps the systems light-weight.
    - but it makes it possible to cheat the system and cause runtime error.
