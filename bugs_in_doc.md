# Bugs in The Document

- ## neo4j-cypher-manual-4.3.pdf

- ## neo4j database version 4.3.5



### 4.5.7. round(), with precision and rounding mode

page 258,(page263 in PDF file)

1. the description of mode "FLOOR" is wrong,which should be "Round towards the smaller neighbor" since the query  '''return round(-1.9,0,"FLOOR"),,, will return -2.0 actually,while according to the original description is "Round towards zero" ,which should return -1.0;
2. the description of mode "HALF_EVEN" is not clear enough;
3. something confused me about the mode "HALF_UP"，which the query '''return round(-0.55,1,"HALF_UP"),,, returns -0.6 but '''return round(-0.5,0,"HALF_UP"),,, returns 0;



### 4.6.2. exp()

page260,(page 265 in PDF file)

the Syntax is wrong as "e(expression)",which should be "exp(expression)";



### 4.6.3. log()

page 261,(page 266 in PDF file)

the Considerations:

- log(0) returns -infinity,which not null;
- if expression is a negative number,log(expression) will return NaN actually,this also happen to log10()and sqrt();

