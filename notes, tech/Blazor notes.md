# Blazor notes

## General Study
https://docs.microsoft.com/en-us/aspnet/core/blazor/debug?view=aspnetcore-6.0&tabs=visual-studio debugging blazor wasm

### Mystery flags
- https://emscripten.org/docs/optimizing/Optimizing-Code.html

## Dark's Usage
### files with thea word 'blazor' or 'webassembly' or 'wasm':
-   Dockerfile
-   .circleci/config.yml
	-   during `build-fsharp-backend` job, some Blazor-related files are `persisted_to_workspace`
		-   backend/static/
			-   BlazorWorker.js
			-   blazor (directory - compiled webassembly stuffs)
-   backend/static/etags.json
-   backend/templates/ui.html
	-   BlazorWorker.js is included in a <script>
-   client/src/appsupport.js
	-   lots of use! TODO: fill this in more
-   fsharp-backend/paket.dependencies
-   fsharp-backend/paket.lock
-   fsharp-backend/src/Wasm/paket.references
-   fsharp-backend/src/Wasm/Wasm.fsproj
-   fsharp-backend/src/Wasm/static/BlazorWorker.js
	-   minimal version, for study: https://gist.github.com/StachuDotNet/fed8983a3626194e970afc7dcfd82ec2
-   fsharp-backend/src/ApiServer/UI.fs
	-   within `let prodHashReplacements`, some blazor-specific things are filtered out. (similar to vendor/)
-   fsharp-backend/src/Prelude/Prelude.fs
	-   isBlazor helper: `let isBlazor = System.OperatingSystem.IsBrowser()`
	-   minor switching to log in one place or another based on ^
-   fsharp-backend/src/Wasm/Wasm.fsproj
-   scripts/build/compile
-   scripts/linting/_check-etags