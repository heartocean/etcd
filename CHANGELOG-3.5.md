

Previous change logs can be found at [CHANGELOG-3.4](https://github.com/etcd-io/etcd/blob/master/CHANGELOG-3.4.md).


<hr>


## v3.5.0 (TBD)

See [code changes](https://github.com/etcd-io/etcd/compare/v3.4.0...v3.5.0) and [v3.5 upgrade guide](https://github.com/etcd-io/etcd/blob/master/Documentation/upgrades/upgrade_3_5.md) for any breaking changes.

**Again, before running upgrades from any previous release, please make sure to read change logs below and [v3.5 upgrade guide](https://github.com/etcd-io/etcd/blob/master/Documentation/upgrades/upgrade_3_5.md).**

### Breaking Changes

- [gRPC gateway](https://github.com/grpc-ecosystem/grpc-gateway) only supports [`/v3`](TODO) endpoint.
  - Deprecated [`/v3beta`](https://github.com/etcd-io/etcd/pull/9298).
  - `curl -L http://localhost:2379/v3beta/kv/put -X POST -d '{"key": "Zm9v", "value": "YmFy"}'` does work in v3.5. Use `curl -L http://localhost:2379/v3/kv/put -X POST -d '{"key": "Zm9v", "value": "YmFy"}'` instead.
- **`etcd --experimental-enable-v2v3` flag has been deprecated.** Use **`etcd --enable-v2v3`** instead.
  - Change [`etcd --experimental-enable-v2v3`](TODO) flag to `etcd --enable-v2v3`; v2 storage emulation is now stable.
- **`etcd --debug` flag has been deprecated.** Use **`etcd --log-level=debug`** instead.
- Remove [`embed.Config.Debug`](https://github.com/etcd-io/etcd/pull/10947).
- **`etcd --log-output` flag has been deprecated.** Use **`etcd --log-outputs`** instead.
- **`etcd --logger=zap --log-outputs=stderr`** is now the default.
- **`etcd --logger=capnslog` flag value has been deprecated.**
- **`etcd --logger=zap --log-outputs=default` flag value is not supported.**.
  - Use `etcd --logger=zap --log-outputs=stderr`.
  - Or, use `etcd --logger=zap --log-outputs=systemd/journal` to send logs to the local systemd journal.
  - Previously, if etcd parent process ID (PPID) is 1 (e.g. run with systemd), `etcd --logger=capnslog --log-outputs=default` redirects server logs to local systemd journal. And if write to journald fails, it writes to `os.Stderr` as a fallback.
  - However, even with PPID 1, it can fail to dial systemd journal (e.g. run embedded etcd with Docker container). Then, [every single log write will fail](https://github.com/etcd-io/etcd/pull/9729) and fall back to `os.Stderr`, which is inefficient.
  - To avoid this problem, systemd journal logging must be configured manually.
- **`etcd --log-outputs=stderr`** is now the default.
- **`etcd --log-package-levels` flag for `capnslog` has been deprecated.** Now, **`etcd --logger=zap --log-outputs=stderr`** is the default.
- **`[CLIENT-URL]/config/local/log` endpoint has been deprecated, as is `etcd --log-package-levels` flag.**
  - `curl http://127.0.0.1:2379/config/local/log -XPUT -d '{"Level":"DEBUG"}'` won't work.
  - Please use `etcd --logger=zap --log-outputs=stderr` instead.
- Deprecated `etcd_debugging_mvcc_db_total_size_in_bytes` Prometheus metric. Use `etcd_mvcc_db_total_size_in_bytes` instead.
- Deprecated `etcd_debugging_mvcc_put_total` Prometheus metric. Use `etcd_mvcc_put_total` instead.
- Deprecated `etcd_debugging_mvcc_delete_total` Prometheus metric. Use `etcd_mvcc_delete_total` instead.
- Deprecated `etcd_debugging_mvcc_txn_total` Prometheus metric. Use `etcd_mvcc_txn_total` instead.
- Deprecated `etcd_debugging_mvcc_range_total` Prometheus metric. Use `etcd_mvcc_range_total` instead.
- Master branch `/version` outputs `3.5.0-pre`, instead of `3.4.0+git`.

### Metrics, Monitoring

See [List of metrics](https://github.com/etcd-io/etcd/tree/master/Documentation/metrics) for all metrics per release.

Note that any `etcd_debugging_*` metrics are experimental and subject to change.

- Deprecated `etcd_debugging_mvcc_db_total_size_in_bytes` Prometheus metric. Use `etcd_mvcc_db_total_size_in_bytes` instead.
- Deprecated `etcd_debugging_mvcc_put_total` Prometheus metric. Use `etcd_mvcc_put_total` instead.
- Deprecated `etcd_debugging_mvcc_delete_total` Prometheus metric. Use `etcd_mvcc_delete_total` instead.
- Deprecated `etcd_debugging_mvcc_txn_total` Prometheus metric. Use `etcd_mvcc_txn_total` instead.
- Deprecated `etcd_debugging_mvcc_range_total` Prometheus metric. Use `etcd_mvcc_range_total` instead.
- Add [`etcd_debugging_mvcc_current_revision`](https://github.com/etcd-io/etcd/pull/11126) Prometheus metric.
- Add [`etcd_debugging_mvcc_compact_revision`](https://github.com/etcd-io/etcd/pull/11126) Prometheus metric.

### etcd server

- [`etcd --enable-v2v3`](TODO) flag is now stable.
  - `etcd --experimental-enable-v2v3` has been deprecated.
  - Added [more v2v3 integration tests](https://github.com/etcd-io/etcd/pull/9634).
  - `etcd --enable-v2=true --enable-v2v3=''` by default, to enable v2 API server that is backed by **v2 store**.
  - `etcd --enable-v2=true --enable-v2v3=/aaa` to enable v2 API server that is backed by **v3 storage**.
  - `etcd --enable-v2=false --enable-v2v3=''` to disable v2 API server.
  - `etcd --enable-v2=false --enable-v2v3=/aaa` to disable v2 API server. TODO: error?
  - Automatically [create parent directory if it does not exist](https://github.com/etcd-io/etcd/pull/9626) (fix [issue#9609](https://github.com/etcd-io/etcd/issues/9609)).
  - v4.0 will configure `etcd --enable-v2=true --enable-v2v3=/aaa` to enable v2 API server that is backed by **v3 storage**.

### Package `embed`

- Remove [`embed.Config.Debug`](https://github.com/etcd-io/etcd/pull/10947).
  - Use `embed.Config.LogLevel` instead.

### gRPC gateway

- [gRPC gateway](https://github.com/grpc-ecosystem/grpc-gateway) only supports [`/v3`](TODO) endpoint.
  - Deprecated [`/v3beta`](https://github.com/etcd-io/etcd/pull/9298).
  - `curl -L http://localhost:2379/v3beta/kv/put -X POST -d '{"key": "Zm9v", "value": "YmFy"}'` does work in v3.5. Use `curl -L http://localhost:2379/v3/kv/put -X POST -d '{"key": "Zm9v", "value": "YmFy"}'` instead.


<hr>

