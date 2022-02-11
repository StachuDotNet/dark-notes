# [3] Version control
Dark removes the complexity of external version control systems like git. Changes to your code are structured using feature flags, wich conceptually unify version control, deployment, and enabling for users.

We also support unlimited undo and redo on all code, though we do not currently support seeing a list of historical versions


## Feature Flags
- In Dark, all changes are made in production, on your real infrastructure
- are similar to ifs (but don't support truthiness - must be exactly true)
- if you'd like to develop for testing, without things immediately going to users, make the changes within a feature flag

### basic structure
```
flag myCondition
then 5
else 6
```

### add a feature flag
- select the code you want to wrap in a feature flag
- open command palette
- select "add-feature-flag"
- choose the 'when' condition
- note: the _new_ code will run when the 'when' condition is true, and the _old_ code will run otherwise
- e.g. the 'when' could be `when request.queryParam.name == "ellen" enabled`
- naturally, if you don't implement the when branch, you'll get an incomplete -> 500

### "commit" or "discard" a feature flag
- you do these from command palette
- commit means to always go to the 'when' condition, and kill the old code
- discard means to just go back to the old code

### Limitations
- currently you can only have one feature flag per component
- there is not yet a way to see the past history of flags
	- (but you can find them on the undo stack)


## PRs
not yet supported