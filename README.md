# YB-TServer Proxy

This is a simple proxy to YugabyteDB Tserver services.

This is useful in non-production use-cases:

1. Accessing DB Service from DBweaver or any other DB query tools.
2. Do not have access to forward port on the DB namespace

## How to use this proxy

1. Edit `yb-tserver-proxy.yaml` and change the tserver service address in the `ConfigMap` / `yb-tserver-proxy` / `nginx.conf` / `stream` part

    Quick way is to use `sed` command to do replacement. Example, if your tserver service is in `yb-demo-orderdb` namespace, you can run following

    ```bash
    sed -i.bak 's/yb-tservers.yb-demo-demodb/yb-tservers.yb-demo-orderdb/' yb-tserver-proxy.yaml
    ```

2. Deploy this `yb-tserver-proxy.yaml`

    ```bash
    kubectl apply -f yb-tserver-proxy.yaml -n my-namespace
    ```

3. Start port-forwarding when you want to access DB

    ```bash
    kubectl port-forward -n my-namesapce svc/yb-tserver-proxy 5433:5433
    ```

    Alternatively, you can change the `type` for `Service` in to LoadBalancer,
    and apply changed. This will create a load balancer service that can be
    connected to any time. Note that this requires your kubernetes cluster to
    have load balancer provisioning capability (MetlaLB, Cloud LB, NSX-T, ASX-ALB, etc.)

4. Access database with any client like ysqlsh

    ```bash
    ysqlsh -c "SELECT inet_server_addr();"
    ```

## Troubleshooting

### Connection to DB stops frequently

Default timeout for the connection is 30s on the LB. You can change that by
setting `proxy_timeout`.
