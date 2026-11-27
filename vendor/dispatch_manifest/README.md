# Dispatch Manifest

Seal one route manifest only after all listed stops have been scanned or explicitly removed.

Logistics, utilities and production · framework · 0.1.2

Framework bird: Northern bald ibis.

## Drive the workflow

Start with `dispatch_manifest_start()`. For each event call `dispatch_manifest_step(snapshot, event, value)`, persist the returned snapshot, and dispatch `dispatch_manifest_command(snapshot)` to your application’s adapter. The host owns I/O, event ordering, duplicate detection and durable command delivery. A matching event produces one command. Rejected events retain the phase and saved quantity. An invalid snapshot yields an empty vector.

The snapshot contains phase ID, saved quantity and last command ID. Event values are whole numbers from zero through one billion. In the table, **x** is the event value and **r** is the saved quantity. Rules match in listed order. To validate a whole history without running effects use `dispatch_manifest_replay(events, values)`. It returns the final snapshot or an empty vector on rejection.

| Current phase | Event | Guard | Next phase | Command | New quantity |
|---|---|---|---|---|---|
| idle | stops | x>0 | loading | open_manifest | x |
| loading | scan | r>0 | loading | accept_scan | r-1 |
| loading | remove | x>0 and x<=r | loading | remove_stops | r-x |
| loading | depart | r=0 | driving | seal_manifest | 0 |
| driving | return | always | done | close_route | 0 |

Phase IDs in order from zero: idle, loading, driving, done.

## Replay your own events

After installing the demo, run the following from its directory. Supply alternating event names and integer values. The command prints each requested action and exits with status 1 at the first rejected transition, or 2 for malformed arguments.

```sh
blyxc native-run main.blyx -- stops 3 scan 0 remove 2 depart 0 return 0
```

## Try the published project

```sh
nest demo dispatch_manifest@0.1.2
blyxc native-run dispatch_manifest-demo/main.blyx
```

Install with `nest add dispatch_manifest@0.1.2` and import `vendor::dispatch_manifest::dispatch_manifest`.

Maintained by Rahul Chaube. [Blyx](https://www.blyx-lang.space) · [GitHub](https://github.com/Rahulchaube1) · [LinkedIn](https://www.linkedin.com/in/rahulchaube1) · [Sponsor](https://github.com/sponsors/Rahulchaube1)
