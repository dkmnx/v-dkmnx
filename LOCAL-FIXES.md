# Local fixes carried on `fixes/local`

`master` in this checkout tracks upstream `vlang/v` and carries no local commits.
Every local fix that is not upstream yet is committed on `fixes/local` instead, so a
new fix never needs a new branch: rebase this branch onto `upstream/master`, add the
commit here, and keep using it.

## Carried commits

Identify them by subject, not by hash: a rebase rewrites hashes.

| Subject                                                                         | Upstream  |
| ------------------------------------------------------------------------------- | --------- |
| fix(cgen): lower direct array element stores through the shared value path      | not sent  |
| net: use SO_RCVTIMEO instead of a select before every blocking read             | not sent  |
| docs: note the local carry branch and its patches (fork only, drop before a PR) | fork only |

## Working here

```sh
git fetch upstream
git rebase upstream/master   # master stays clean, this branch moves
./v self                     # rebuild the compiler in place after a rebase
```

A pull request carries one commit, not this branch: cherry-pick that commit onto a
fresh branch from `upstream/master`, so this carry history never reaches a PR. Drop
this file from such a branch, it is fork-only.

## Next candidates

- `net`: `TcpListener.accept_only()` still waits with `select()` per accept, which is
  redundant for a blocking listener with an infinite accept timeout.
- `net`: `UdpConn.read_ptr()` has the same select-before-recv pattern as TCP had.
- `cgen`: element stores in *safe* code still call `array__set()` per element; an
  inlined bounds check would let the C compiler optimize those loops too.
