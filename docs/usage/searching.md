### Domains
**About domain syntax**

Domain syntax is : array( array( array(operand, operator, operand)[, array(operand, operator, operand) [, ...]]) [, array( array(operand, operator, operand)[, array(operand, operator, operand) [, ...]])])

It is an array of several series of clauses joined by logical AND clauses themselves joined by logical OR clauses (disjunctions of conjunctions).

 i.e.: (clause[, AND clause [, AND ...]]) [ OR (clause[, AND clause [, AND ...]]) [ OR ...]]

* 	accepted operators are : '=', '<', '>',' <=', '>=', '<>', 'like' (case-sensitive), 'ilike' (case-insensitive), 'in', 'contains'
* 	example : array( array( array('title', 'like', '%foo%'), array('id', 'in', array(1,2,18)) ) )





