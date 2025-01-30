FROM iamseth/oracledb_exporter:latest

COPY metrics.toml /

ENTRYPOINT ["/oracledb_exporter", "--custom.metrics", "/custom-metrics.toml"]