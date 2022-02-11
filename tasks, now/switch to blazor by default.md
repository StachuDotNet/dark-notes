# Switch to Blazor in the client
(change the default. right now you need `use-blazor=true` query param in client to use)
https://github.com/darklang/dark/issues/3254

## TODOs
 - [x] Get blazor working on darklang.com
 - [x] switch the client's default to blazor
 - [x] switch integration tests over to using blazor
 - [ ] switch to F# backend by default (port 9000)
	 - [x] and allow for an --ocaml option
	 - [ ] figure out why F# port by default isn't working
 - [ ] ensure when blazor fails that we get a rollbar
	 - is this referring to...
	 - [ ] the blazor worker failing, or
	 - [ ] the actual blazor usage
	 - (maybe both?)
 - [ ] add changelog entry explaining how to change back
	 - [ ] https://docs.darklang.com/changelog
	 - [ ] https://github.com/darklang/docs/blob/main/docs/changelog.md

## Notes
- https://docs.microsoft.com/en-us/aspnet/core/blazor/fundamentals/handle-errors?view=aspnetcore-6.0
- read this stuff about logging https://docs.microsoft.com/en-us/aspnet/core/blazor/fundamentals/logging?view=aspnetcore-6.0
---

 1 failed
    [chromium] › tests.ts:750:3 › Integration Tests › int_add_with_float_error_includes_fnname =====
  1 flaky
    [chromium] › tests.ts:1238:3 › Integration Tests › upload_pkg_fn_as_admin ======================
  69 passed (8m)


  the int-addition test makes sense

  