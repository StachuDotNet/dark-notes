# Optimize/Trim Blazor
[GitHub Issue 3168](https://github.com/darklang/dark/issues/3168)

https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-workload-install

`du -sh fsharp-backend/Build/`

- try switching to 6.0.2, 6.0.3, or 7.0.1
- https://github.com/darklang/dark/commit/cc88c3ff6badf3b34f26cf15e5c80d4e1e7c51c7#diff-dd2c0eb6ea5cfc6c4bd4eac30934e2d5746747af48fef6da689e85b752f39557L354
	- docs and such
		- https://github.com/dotnet/core/tree/main/release-notes
		- https://github.com/dotnet/installer/blob/main/README.md#table <- important
		- https://github.com/dotnet/core/issues/7106 (what's new in .net 7 preview 1)
		- https://github.com/dotnet/sdk/issues/23403 ugh this may be blocking us
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
- https://github.com/dotnet/aspnetcore/issues/35633
- https://github.com/dotnet/sdk/issues/23361
- https://github.com/dotnet/maui/issues/3099


## Running integration tests w/ various scenarios

```
rm -rf backend/static/blazor \
  && rm backend/static/BlazorWorker.js \
  && ./scripts/build/clear-dotnet-build \
  && ./scripts/build/compile-project fsharp-backend --optimize
```

`time ./integration-tests/run.sh --retry`

### w/ blazor, no optimize, no aot, 6.0.1
  23 failed
    [chromium] › tests.ts:690:3 › Integration Tests › rename_function ==============================
    [chromium] › tests.ts:708:3 › Integration Tests › execute_function_works =======================
    [chromium] › tests.ts:747:3 › Integration Tests › int_add_with_float_error_includes_fnname =====
    [chromium] › tests.ts:815:3 › Integration Tests › function_analysis_works ======================
    [chromium] › tests.ts:846:3 › Integration Tests › fn_page_to_handler_pos =======================
    [chromium] › tests.ts:883:3 › Integration Tests › extract_from_function ========================
    [chromium] › tests.ts:928:3 › Integration Tests › fluid_click_2x_in_function_places_cursor =====
    [chromium] › tests.ts:938:3 › Integration Tests › fluid_doubleclick_selects_token ==============
    [chromium] › tests.ts:945:3 › Integration Tests › fluid_doubleclick_selects_word_in_string =====
    [chromium] › tests.ts:952:3 › Integration Tests › fluid_doubleclick_selects_entire_fnname ======
    [chromium] › tests.ts:961:3 › Integration Tests › fluid_doubleclick_with_alt_selects_expression 
    [chromium] › tests.ts:971:3 › Integration Tests › fluid_shift_right_selects_chars_in_front =====
    [chromium] › tests.ts:981:3 › Integration Tests › fluid_shift_left_selects_chars_at_back =======
    [chromium] › tests.ts:1002:3 › Integration Tests › fluid_ctrl_left_on_string ===================
    [chromium] › tests.ts:1010:3 › Integration Tests › fluid_ctrl_right_on_string ==================
    [chromium] › tests.ts:1018:3 › Integration Tests › fluid_ctrl_left_on_empty_match ==============
    [chromium] › tests.ts:1063:3 › Integration Tests › empty_fn_never_called_result ================
    [chromium] › tests.ts:1078:3 › Integration Tests › empty_fn_been_called_result =================
    [chromium] › tests.ts:1121:3 › Integration Tests › fluid_fn_pg_change ==========================
    [chromium] › tests.ts:1189:3 › Integration Tests › fluid_test_copy_request_as_curl =============
    [chromium] › tests.ts:1235:3 › Integration Tests › upload_pkg_fn_as_admin ======================
    [chromium] › tests.ts:1375:3 › Integration Tests › record_consent_saved_across_canvases ========
    [chromium] › tests.ts:1557:3 › Integration Tests › package_function_references_work ============
  48 passed (24m)

real    23m33.746s
user    10m19.653s
sys     1m21.484s

### w/ blazor, aot, 6.0.1, _not_ publish folder
(just wwwroot)

### w/ blazor, aot, 6.0.1, publish folder
