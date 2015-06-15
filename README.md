# Object Borrowed Properties for ECMAScript

ECMAScript 6 introduces ability to destructure object into list of variables and shorthand syntax for object properties.

This proposal introduces ability to extract given properties of object into new one without need for destructuring into intermediate variables. This allows to enforce needed static structure (hidden class) of object being created so that it's easier to reason about both for developer and for JITs.

## Examples

### Regular properties

Syntax is similar to one already implemented in C# ([read more](https://msdn.microsoft.com/en-us/library/bb384062.aspx)) and looks like following:

```javascript
var badVector = { y: 2, x: 1, z: 3 };

var vector1 = { badVector.x, badVector.y };
var vector2 = { badVector['x'], badVector['y'] };
```

This desugars into following:

```javascript
var badVector = { y: 2, x: 1, z: 3 };

var vector1 = { x: badVector.x, y: badVector.y };
var vector2 = { 'x': badVector['x'], 'y': badVector['y'] };
```

### Computed properties

It's also possible to use ECMAScript 6 computed properties syntax for extraction:

```javascript
var badVector = { beta: 2, alpha: 1, gamma: 3 };

var vector = {
    badVector['alp' + 'ha'],
    badVector['be' + 'ta']
};
```

This desugars into following:

```javascript
var badVector = { beta: 2, alpha: 1, gamma: 3 };

var _temp1, _temp2;

var vector = {
    [_temp1 = 'alp' + 'ha']: badVector[_temp1],
    [_temp2 = 'be' + 'ta']: badVector[_temp2]
};
```

## Syntax

*BorrowedOwnerExpression*<sub>[Yield]</sub>:
* **this**
* *IdentifierReference*<sub>[?Yield]</sub>
* *CoverParenthesizedExpressionAndArrowParameterList*<sub>[?Yield]</sub>

*BorrowedMemberExpression*<sub>[Yield]</sub>:
* *BorrowedOwnerExpression*<sub>[?Yield]</sub>
* *BorrowedMemberExpression*<sub>[?Yield]</sub> [ *Expression*<sub>[In, ?Yield]</sub> ]
* *BorrowedMemberExpression*<sub>[?Yield]</sub> . *IdentifierName*
* *SuperProperty*
* *MetaProperty*

*PropertyDefinition*<sub>[Yield]:
* *BorrowedMemberExpression*<sub>[?Yield]</sub>
* *CoverInitializedName*<sub>[?Yield]</sub>
* *PropertyName*<sub>[?Yield]</sub> : *AssignmentExpression*<sub>[In, ?Yield]</sub>
* *MethodDefintion*<sub>[?Yield]</sub>

*CoverInitializedName*<sub>[Yield]</sub>:
* *BorrowedMemberExpression*<sub>[?Yield]</sub> *Initializer*<sub>[In, ?Yield]</sub>
