# history

The record of how this repository's history was extracted from the monorepo,
[The Test Cabinet](https://github.com/TheClockwyrks/TheTestCabinet), where the
crates here lived until the extraction. Nothing builds or tests against these
files; they are kept so the provenance outlives the scratch clone the
extraction ran in.

## The extraction

The history below the scaffold commit is the monorepo's `nightly` at
`be65122b5d899a448b59b1ce6c6f5a3e778b4419`, filtered to the paths in
[`paths.txt`](paths.txt) by git filter-repo 2.47.0:

```sh
git clone -q --no-local --single-branch --branch nightly --no-tags <the-test-cabinet> x
cd x
uvx --from git-filter-repo==2.47.0 git-filter-repo --force \
  --paths-from-file ../paths.txt --prune-empty always --preserve-commit-hashes
```

Of the 6,971 commits reachable from that commit, 32 touched a path in the list
and were kept (5 of them merges); the rest were pruned. No tag was carried. The
tip tree of every moved directory hashes the same as the monorepo's at that
commit:

```text
crates/model-core/  9e9f544bc48bf38f4db635a881f40ef7dfe1e035
crates/voxel-mesh/  7640cf2c3f7c94d26943957135c3d553cb285398
crates/model-skin/  34c71c25a1dae7cc9010a1c2b1a4ef644596b654
```

The paths are kept where they were in the monorepo, so the filter needed no
rename and the path dependencies between the three crates work as they did.

## The files

- [`paths.txt`](paths.txt) is the list handed to `--paths-from-file`
  (sha256 `c82ccbaf52eadd084ac052b2db84b72befb3ce6d1514d59ebf420f3f58e9ba43`).
  It is generated, not hand-written: the three moved directories
  (`crates/model-core/`, `crates/voxel-mesh/`, `crates/model-skin/`), and
  every path a tracked file under them had before a rename or copy, collected
  by `git log --follow -M -C --name-status` over each of the 36 files.
- [`commit-map`](commit-map) is filter-repo's map from each monorepo commit (left)
  to the commit it became here (right), all zeros for a pruned one.
  `--preserve-commit-hashes` left the commit ids quoted in the messages as they
  were, so an id in a message below the scaffold commit names a monorepo commit,
  and this map translates it.

## The live-path review

Outside the three moved directories the list names two paths,
`crates/voxel/src/color.rs` and `crates/voxel/src/rig.rs`, which
`crates/model-core/src/color.rs` and `crates/model-core/src/rig.rs` had before
the model core was split out of the voxel crate. The split moved them, so
neither is in the monorepo's tree at the extraction commit: the filtered tip
holds the three directories alone and the scaffold commit deletes nothing.
