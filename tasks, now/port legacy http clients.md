# Port Legacy HTTP Clients

## TODOs:
- resolve remaining unit tests
- write fuzzer tests
	- review existing fuzzer tests
	- write some generators
		- verb
		- URL?
		- query params
		- body
		- headers
-   add notes about _ster in some readme
-   review all relevant readmes and adjust with your learning
-   adjust the docs repo with various learnings
	
## Notes
-   similarly to above, v0 of the libhttpclient had some wrapped_ fns where seemginly only try/catch was taken care of
    -   the 'non-wrapped' versions of these fns were used by v0 of dark-exposed fns,
    -   while the 'wrapped' versions were used by v1 of dark-exposed fns
    -   again, should we here _intentionally_ not catch runtime exceptions in the early code

-   [tree.pdf](https://dynalist.io/u/eHqLYJmLCfxDValn-ikcDvqZ)
-   todo: create documentation on how to expose _all_ fns, includind deprecated
    -   or make this a config somewhere, or a secret querystring thing, or a client-side feature

### OCaml source code, for various comparisons
#### Dark-exposed fns
        
#### Libhttpclient  
(middle-man helpers between Dark-exposed fns and "internal" http client)

-  [source, 'vCurrent'](https://github.com/darklang/dark/blob/main/backend/libbackend/libhttpclient.ml#L11-L228) (defined within vCurrent of the Dark-exposed fns)
-  [source, LibhttpclientV2](https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L996-L1124) (used only by v2 fns
- [source, LibhttpclientV1](https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L866-L994)
- [source, LibhttpclientV0](https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L727-L864)
- (general quick note - Httpclient exists largely in its complex form in Ocaml because apparently Ocaml doesn't have a great native http client module. F# _does_ have a good HTTP Client, so a lot of the 'custom' stuff might be able to disappear?)
- [source diffs](https://gist.github.com/StachuDotNet/6727e5c4d0d88e0280cfc835a89fedaa/revisions)
	- v2 and vCurrent
	- v1 vs v2
	- v0 vs v1
		- (lots of changes..)
		- v1 introduces the 'params'
		-   v0 calls HttpclientV0.http_call
		-   v1 calls HttpclientV1.http_call
		-   related to ^, v1 returns `result, header, code` tuple, while v0 doesn't include `code` portion
		-   v1 has some try/catching that v0 didn't have
		-   v1 doesn't have the [`wrapped_send_request`, `wrapped_call`, `wrapped_call_no_body`] fns that v0 exposes - maybe the 'wrapping' is the try/catch that's taken care of in the 'main' fn now?

#### HttpClient  
(the _really_ underlying http thing)

- v2 source https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L493-L725
- v1 source https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L248-L491
- v0 source https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L9-L246
- source and diffs https://gist.github.com/StachuDotNet/28e8051d422db3524a99ff8e639d1ebf/revisions
- some notes
	-   between vCurrent and v2
	-   between v2 and v1
		-   v1 took on the responsibility of raising/returning an exception if the (http status) 'code' is a non-200
			-   v2 drops this responsibility for some reason
	-   between v1 and v0
		-   v0 didn't included a(n http status) code in its result, but v1 does
		-   v1 accepts an optional raw_bytes bool flag
			-   if provided, ... idk. maybe not relevant w/ the ported stuff