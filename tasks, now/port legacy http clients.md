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

## OCaml source code, for various comparisons
### Dark-exposed fns

- Dark-exposed fns
    - source: https://github.com/darklang/dark/blob/main/backend/libbackend/libhttpclient.ml#L231-L759
        - this^ houses all v0-v5 Dark-accessible functions
    - source diffs: https://gist.github.com/StachuDotNet/3aab0707b7d27289718511ad083ee6d2/revisions
    - v5 has its own `call` fn and "libhttpclient' within the file itself, unlike the other versions
    - between v4 and v5,
        - the only difference is that they use different libhttpclient versions
            - v4 uses libhttpclient v2
            - v5 uses libhttpclient vCurrent
    - between v3 and v4,
        - the only difference is that they use different libhttpclient versions
            - v4 uses libhttpclient v2
            - v3 uses libhttpclient v1
    - between v2 and v3,
        - some fn descriptions were improved
        - v3 uses libhttpclientv1.call and .call_no_body
        - v2 uses Libhttpclientv0.wrapped_call and .wrapped_call_no_body
        - no other differences - just need to look into libhttpclientvX diffs
    - between v1 and v2
        - some fn descriptions were improved
        - they are both using libhttpclientv0
        - v1 uses .call and call_no_body; v2 uses wrapped_call and wrapped_call_no_body
        - that's^ the only difference
            - (maybe this is the try/catch thing?)
    - between v0 and v1
        - some fn descriptions were improved
        - v0 doesn't actually have "versioning" set up in the fn names (it's just HttpClient::post, without any _v0 suffix
        - different seralizers seem to be used - is this going to be a problem? :/
            - v0 uses Libexecution.Legacy.PrettyRequestJsonv0.to_pretty_request_json_v0
                - #todo the underlying ocaml code here seems to have not yet been converted, and is a bit OCaml-rough. reach out to Paul for assist?
                    - or leave for CLEANUP before final merge, leaving some tests commented out
            - v1 uses Dval.to_pretty_machine_json_v1
        - params_no_body and call_no_body seems to have been introduced at this time - the GET and DELETE and OPTIONS and HEAD request fns use such in v1
    - https://gist.github.com/StachuDotNet/b1d3901b1019e911fce797da3772472c these fns also exist; Paul extracted them from the normal http client stuff when porting to F#. there's a v0 (unsuffix'd) and a v1 here. anyway, I'd consider these 'handled' at this point
        
### Libhttpclient  
(middle-man helpers between Dark-exposed fns and "internal" http client)

- Libhttpclient
  middle-man helpers between Dark-exposed fns and "internal" http client
    - source
        - 'vCurrent' (defined within vCurrent of the Dark-exposed fns)
            - https://github.com/darklang/dark/blob/main/backend/libbackend/libhttpclient.ml#L11-L228
        - LibhttpclientV2 (used only by v2 fns)
            - https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L996-L1124
        - LibhttpclientV1
            - https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L866-L994
        - LibhttpclientV0
            - https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L727-L864
            - #todo need to learn things here about wrapped_ stuff
    - (general quick note - Httpclient exists largely in its complex form in Ocaml because apparently Ocaml doesn't have a great native http client module. F# _does_ have a good HTTP Client, so a lot of the 'custom' stuff might be able to disappear?)
    - source diffs
        - https://gist.github.com/StachuDotNet/6727e5c4d0d88e0280cfc835a89fedaa/revisions
            - this excludes the `encode_basic_auth` fns found in v1+ - F# solution has already ported these to a separate 'module'
        - v2 and vCurrent
            - vCurrent uses a different (let's call it _also_ vCurrent)  version of the underlying HttpClient.
            - `has_plaintext_header` introduced in vCurrent
                - returns `true` iff there's an existing header of plain/text
            - `with_default_content_type` introduced in vCurrent
                - if the Dark-user request included a Content-Type, use it - otherwise, use the given 'default' content type
            - `encode_request_body` introduced in vCurrent
                - encodes the Dark-user's given request body (if any), so it's safe for use across the internet:
                - if the request body is an Object (DObj) and we've been provided a _form_ content-type, then use `Dval.to_form_encoding`
                - otherwise, if the request body is a String (DStr), don't do _anything_
                    - also, set the content-type to plain if not given
                - _otherwise_, 
                - _final_ otherwise, assume json
                    - use Dval.to_pretty_machien_json_v1
                    - set content-type to application/json if not given
            - vCurrent also adjusted `send_request` (which is the main big should-be-private fn used by `call` and `call_no_body`)
                - https://gist.github.com/StachuDotNet/6727e5c4d0d88e0280cfc835a89fedaa/revisions#diff-20e11b202b4d3036912bd20010e3188e0d760517e70c191a528d7bce779e3457L24-L78
                - instead of taking in `json_fn (dval->string)` and `body (dval)` separately, vCurrent only takes in `body (dval option)`
                    - special note: _option_ - v2 forces a non-option
                - something about munged headers!?
                - some changes are def. relevant to the request encoding
                    - v2 did it simple - if a form header was included, for mencoding was the way. otherwise, json. vCurrent is slightly more sophisticated
                - vCurrent returns an "error" in its DObj response, where v2 doesn't
                    - ("error" is the res.error - probably empty if irrelevant)
                    - (so, I can just strip that when tidying v4 - or maybe I already did that)
            - vCurrent `call` and `call_no_body` no longer allow you to pass in json_fn - just body (which is `option`al)
        - v1 vs v2
            - v1 uses HttpclientV1.http_call
            - v2 uses HttpclientV2.http_call
            - no other differences
        - v0 vs v1
            - (lots of changes..)
            - v1 introduces the 'params' 
            - v0 calls HttpclientV0.http_call 
            - v1 calls HttpclientV1.http_call 
            - related to ^, v1 returns `result, header, code` tuple, while v0 doesn't include `code` portion
            - v1 has some try/catching that v0 didn't have 
            - v1 doesn't have the [`wrapped_send_request`, `wrapped_call`, `wrapped_call_no_body`] fns that v0 exposes - maybe the 'wrapping' is the try/catch that's taken care of in the 'main' fn now?
    - conclusions

### HttpClient  
(the _really_ underlying http thing)

- Httpclient
  (the _really_ underlying http thing)
    - source
        - v2 https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L493-L725
        - v1 https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L248-L491
        - v0 https://github.com/darklang/dark/blob/main/backend/libbackend/legacy.ml#L9-L246
    - source and diffs
        - https://gist.github.com/StachuDotNet/28e8051d422db3524a99ff8e639d1ebf/revisions 
        - between vCurrent and v2
            - vCurrent's `http_call` accepts a string _option_ as input for body, rather than string
            - vCurrent's `http_call` (the main fn here) returns a more sophisticated type
                - v2 returns a tuple of (resp_body, resp_headers, code)
                - vCurrent returns a record of { body, code, headers, _errors_ }
            - vCurrent's response is a Result rather than a flat response
            - type http_result =
                - { body : string
                - ; code : int
                - ; headers : (string * string) list
                - ; error : string }
            - type curl_error = { url : string; error : string; code : int }
            - for some reason vCurrent defines its own 'verb' type (GET, etc.) that it expects as input rather than the OCaml-built-in Httpclient.verb
        - between v2 and v1
            - v1 took on the responsibility of raising/returning an exception if the (http status) 'code' is a non-200
                - v2 drops this responsibility for some reason
        - between v1 and v0
            - v0 didn't included a(n http status) code in its result, but v1 does
            - v1 accepts an optional raw_bytes bool flag
                - if provided, ... idk. maybe not relevant w/ the ported stuff