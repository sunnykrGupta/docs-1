# Profile an HTTP Endpoint

> **Note**: This feature is only supported in Kubecost v1.101+.

A CPU profile spanning the duration of a single HTTP endpoint call can be automatically generated by setting the `X-Kubecost-CPU-Profile` header to any value during the call. The profile is automatically started and ended by handler middleware.

Once the profile is complete, it can be downloaded by visiting the `/model/httpprofile/cpu` endpoint. If visited in a browser, it should automatically download as a file. This profile should then be attached to the relevant support ticket or GitHub issue for common usage.

## Example usage

Here's a full example, including some extra metadata from `curl` to aid debugging.

{% code overflow="wrap" %}
```
curl -G 'localhost:9090/model/savings/requestSizingV2' \
    -H 'X-Kubecost-CPU-Profile: true' \
    -w "Connect: %{time_connect} TTFB: %{time_starttransfer} Total time: %{time_total} \n"
```
{% endcode %}

```
curl -G 'localhost:9090/model/httpprofile/cpu' \
    -o '/tmp/profile.cpu.out'
```

And to view the profile, standard Go tools work:

```
go tool pprof -http 127.0.0.1:9367 /tmp/profile.cpu.out
```

## Notes

* There is a maximum profile duration of one hour. If your query duration exceeds this, the profile will halt prematurely. This is to avoid filling up the disk.
* Only the most-recently-triggered profile is saved in order to avoid filling up the disk.
* Any saved profile disappears on Pod restart.