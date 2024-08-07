# evtx_dumper
The main binary utility provided with this crate is evtx_dump, and it provides a quick way to convert .evtx files to different output formats.  Some examples

# USAGE
Some examples
  - `evtx_dump <evtx_file>` will dump contents of evtx records as xml.
  - `evtx_dump -o json <evtx_file>` will dump contents of evtx records as JSON. 
  - `evtx_dump -f <output_file> -o json <input_file>` will dump contents of evtx records as JSON to a given file.


  `evtx_dump` can be combined with [fd](https://github.com/sharkdp/fd) for convenient batch processing of files:
  - `fd -e evtx -x evtx_dump -o jsonl` will scan a folder and dump all evtx files to a single jsonlines file.
  - `fd -e evtx -x evtx_dump '{}' -f '{.}.xml` will create an xml file next to each evtx file, for all files in folder recursively!
  - If the source of the file needs to be added to json, `xargs` (or `gxargs` on mac) and `jq` can be used: `fd -a -e evtx | xargs -I input sh -c "evtx_dump -o jsonl input | jq --arg path "input" '. + {path: \$path}'"`
  
**Note:** by default, `evtx_dump` will try to utilize multithreading, this means that the records may be returned out of order.

To force single threaded usage (which will also ensure order), `-t 1` can be passed.

## Example usage (as library):
```rust
use evtx::EvtxParser;
use std::path::PathBuf;

// Change this to a path of your .evtx sample. 
let fp = PathBuf::from(format!("{}/samples/security.evtx", std::env::var("CARGO_MANIFEST_DIR").unwrap())); 

let mut parser = EvtxParser::from_path(fp).unwrap();
for record in parser.records() {
    match record {
        Ok(r) => println!("Record {}\n{}", r.event_record_id, r.data),
        Err(e) => eprintln!("{}", e),
    }
}
```
