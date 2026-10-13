# llab-data -- measured rows from a local llama.cpp zoo

Machine-readable results from benchmarking local GGUF models on CPU
(llama-bench + llama-perplexity + chat sessions), recorded by the llab tool.

Every row is append-only NDJSON, one measurement per line, stamped with
fingerprints so results can never be attributed to the wrong artifact.

## Files

| file | what |
|---|---|
| `data/speed_rows.ndjson` | llama-bench rows: model, engine, threads, kind (ppN/tgN), avg_ts, gguf_fp, exe_fp, taps flag |
| `data/ppl_rows.ndjson` | perplexity A/B rows: model, engine, corpus, ctx, threads, perplexity, stderr, wall_s |
| `data/chat_sessions.ndjson` | every chat session through the lab proxy: port, actor, wall_ms, tokens, tps, finish, prompt hash |
| `data/ppl_corpus.txt` | the frozen perplexity corpus (90,726 bytes, deterministic build) |
| `data/perplexity/*.txt` | raw llama-perplexity logs with per-chunk scores |
| `data/doctor-sweep-*.txt` | GGUF header forensics sweep (gguf-doctor) |

## Schema notes

- `gguf_fp` / `exe_fp`: FNV-1a over head+tail 64KB of the file. Same fp = same
  artifact; different fp = results are not comparable.
- `engine`: which local build served the row (`stock` = unmodified upstream
  build, `mod` = the daavile fx fork, `prism` = retired ternary experiment).
- `kind`: `pp512` = prompt processing 512 tokens; `tg128` = generation 128
  tokens; same pattern for other sizes. Higher tok/s = better.
- `n`: token count for the row (matches the kind's N).

## Reference machine

AMD Ryzen 5 5600G (6c/12t), 2x 3600 DDR4, Windows 11. All benches
BelowNormal priority, `-ngl 0`, unless a row says otherwise. CPU-only rows.

## Headline numbers

- LFM2.5-230M Q8: pp 511 / tg 100 tok/s (t6)
- Qwen3.5-2B Q4_K_M: pp ~112-127 / tg ~18 (stock)
- LFM2.5-8B-A1B Q4_K_M: ~87 / ~19 (stock, t10)
- Fork vs stock perplexity on same GGUF: bit-identical (12.0265 both)

## License

Rows and corpus: CC0 / public domain. Corpus is built from the authors' own
documentation.
