# Immutable deep merge

Rules:

* Scalars overwrite (last writer wins)
* Arrays are replaced
* Plain objects are merged recursively
* Always returns a fresh object tree so React Fast Refresh sees the change
