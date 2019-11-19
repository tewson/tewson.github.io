---
layout: post
title: "jscodeshift Snippet - Printing a Whole MemberExpression"
date: 2019-11-19
---

I've been writing codemods using [jscodeshift](https://github.com/facebook/jscodeshift). I find that while the main goal of codemods is to refactor code, I often have to print some output either for debugging or making sure I'm on the right path.

One of the things I had to do recently was to print a whole `MemberExpression` based on a module name I was looking for. For example, consider the following code snippet.

<!--more-->

```js
myUtils.doAction();
// ...
myUtils.subModule.doAction();
// ...
myUtils[selectedSubModule][selectedAction]();
// ...
myUtils[selectedSubModule][SOME_NAMESPACE.CONSTANT_NAME];
```

From the code above, I want to be able to print, in full, all the `MemberExpression`s related to `myUtils`. Below is the desired output.

```
myUtils.doAction
myUtils.subModule.doAction
myUtils[selectedSubModule][selectedAction]
myUtils[selectedSubModule][SOME_NAMESPACE.CONSTANT_NAME]
```

This turns out to be a very simple thing to do. We just have to do the following.

1. Locate a `MemberExpression` that has an `Identifier` named `myUtils` as the `object`.
2. If the `MemberExpression` from (1) is part of another `MemberExpression`, find the root `MemberExpression`.

   (For example, the `myUtils.subModule`, though a `MemberExpression`, is still the `object` in `myUtils.subModule.doAction`.)

3. Print the root `MemberExpression` using `toSource()`.

We should end up with something like below.

```js
// print-root-member-expressions.js
export default function printRootMemberExpressions(file, api) {
  const j = api.jscodeshift;

  const getRootMemberExpressionPath = p => {
    if (j.MemberExpression.check(p.parent.node)) {
      return p.parent;
    }

    return p;
  };

  const getRootMemberExpressionSource = p => {
    const rootMemberExpressionPath = getRootMemberExpressionPath(p);
    return j(rootMemberExpressionPath).toSource();
  };

  j(file.source)
    .find(j.MemberExpression, {
      object: {
        name: "myUtils"
      }
    })
    .forEach(path => {
      console.log(getRootMemberExpressionSource(path));
    });
}
```

See it in action on [AST Explorer](https://astexplorer.net/#/gist/142f6ee1b3326c3d1d3f917521af3ec8/87ac7a65743e600022df19971d68cbd0f7a5c85c). Check the console log.
