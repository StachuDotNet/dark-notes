# Switch to Blazor in the client
(change the default. right now you need `use-blazor=true` query param in client to use)
https://github.com/darklang/dark/issues/3254

## TODOs
- [ ] switch integration tests over to using blazor
- [ ] ensure when blazor fails that we get a rollbar
- [ ] add changelog entry explaining how to change back
- [ ] switch the client's default to blazor

## Notes
- https://docs.microsoft.com/en-us/aspnet/core/blazor/fundamentals/handle-errors?view=aspnetcore-6.0
- read this stuff about logging https://docs.microsoft.com/en-us/aspnet/core/blazor/fundamentals/logging?view=aspnetcore-6.0