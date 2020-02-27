Install Comby: https://github.com/comby-tools/comby/#install-pre-built-binaries

```
COMBY_M="$(cat <<"MATCH"
```
:[code]
```
MATCH
)"
COMBY_R="$(cat <<"REWRITE"
:[code]
REWRITE
)"
# Install comby with `bash <(curl -sL get.comby.dev)` or see github.com/comby-tools/comby && \
comby "$COMBY_M" "$COMBY_R"  .txt -stats
```