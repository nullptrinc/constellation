# Constellation

Constellation is the hardware abstraction of Starry, after QNX PPS on
BlackBerry 10. System services are the only code that touches drivers; they
publish state as small typed objects in the `/blinks` namespace through the
`constellationd` broker (`/run/constellation.sock`), and applications read
and subscribe to them.

```
$ blinks get /blinks/power/battery
@/blinks/power/battery
present:b:true
level:n:98
status::not-charging
charging:b:false
plugged:b:true
voltage:n:4.274228
temperature:n:28.3
```

- Attributes are `name:type:value`: `n` number, `b` boolean, empty for a string.
  Publishing merges attributes; `-name` removes one.
- The connection that creates an object owns it until it disconnects.
- Only root may write the app registry (`/blinks/apps/`) and the battery
  (`/blinks/power/battery`).
- Subscribers get the whole object on every change. A slow subscriber skips
  intermediate states but always gets the latest one. A path ending in `/`
  subscribes to everything under it.
- Objects live in the broker's memory: services publish theirs again after a
  broker restart; values set by hand are gone.

The wire protocol is described at the top of `daemon/constellationd.c`.

## Pieces

| Path | |
|---|---|
| `include/constellation.h`, `lib/` | client library (`pkg-config constellation`) |
| `daemon/constellationd.c` | the broker |
| `tools/blinks.c` | `blinks ls\|get\|set\|del\|watch` from a shell |
| `services/power.c` | `constellation-power`: publishes `/blinks/power/battery` |
| `data/constellation.init.in` | sysvinit service for the broker and services |

QML applications use `import Constellation 1.0` from
[Stardust](https://github.com/nullptrinc/stardust).

## Building

```sh
meson setup build --prefix=/opt/starry
ninja -C build
```

## License

MIT, see [LICENSE](LICENSE).
