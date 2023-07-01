## Section 1

- Granularity Problem : the sizes of the types we are working with are different.
  - we will end up creating different structure in the different sides(ex:address).
- Inheritance is not well represented in the relational model.
  - relational db don't posses a standard way to represent polymorphic associations. one fk can only reference on table u cannot define one for multiples tables. <br />
    <img src="./images/polymorphic-association-inheritance-problem.png" alt="polymorphic-association-inheritance-problem" style="width:400px;"/> <br>
- Identity Problem:
    - OOP: refrence identity(==), Logical identity(equal method)
    - DB: Primary Key & same table.
        - same row can be mapped to multiple java object.
- Association Problem:
    - DB: FK, can MtO, OtO. MtM will requires new table.
    - OOP: directional reference, can have oneToOne, OtM, MtM.
- Data Navigation:
    - To use join effectively, we have to know the part of the <br> network we need to access when we retrieve  the initial instance <br> before effectively navigation the object network. 
 