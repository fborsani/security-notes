# XPath injection

## XPath

### Operator list

#### Select nodes

```
/             #select root
//            #select all nodes regardless of position
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
```

#### Complex select

```
```

## Injection attacks

