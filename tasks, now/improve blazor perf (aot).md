# Optimize/Trim Blazor
[GitHub Issue 3168](https://github.com/darklang/dark/issues/3168)

https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-workload-install

- try switching to 6.0.2, 6.0.3, or 7.0.1
	- docs and such
		- https://github.com/dotnet/core/tree/main/release-notes
		- https://github.com/dotnet/installer/blob/main/README.md#installers-and-binaries
		- https://github.com/dotnet/core/issues/7106 (what's new in .net 7 preview 1)
	- some relevant files in Dark
		- global.json
		- dockerfile
		- 
-  get real stack traces
	- https://stackoverflow.com/questions/55960859/no-stacktrace-in-blazor
- mystery .fsproj flags [[Blazor notes#Mystery flags]]
- https://stackoverflow.com/questions/63993294/there-was-no-runtime-pack-for-microsoft-aspnetcore-app-available-for-the-specifi
- https://github.com/dotnet/aspnetcore/issues/37690
- measure the current pains
- https://docs.microsoft.com/en-us/aspnet/core/blazor/performance?view=aspnetcore-6.0
- https://docs.microsoft.com/en-us/aspnet/core/blazor/host-and-deploy/configure-trimmer?view=aspnetcore-6.0
- https://github.com/dotnet/runtime/blob/main/src/mono/wasm/build/WasmApp.targets
- https://github.com/dotnet/docs/issues/27395
- https://docs.microsoft.com/en-us/aspnet/core/blazor/host-and-deploy/webassembly?view=aspnetcore-6.0#ahead-of-time-aot-compilation
- what's the "other thing" that we're on bleeding edge for?
- https://docs.microsoft.com/en-us/aspnet/core/blazor/webassembly-lazy-load-assemblies?view=aspnetcore-6.0 lazy loading assemblies
- https://github.com/dotnet/sdk/pull/20961
- https://github.com/dotnet/aspnetcore/issues/37690
- https://github.com/dotnet/aspnetcore/issues/35633