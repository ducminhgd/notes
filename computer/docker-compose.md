# Docker compose

## Ports order

`HOST_PORT:CONTAINER_PORT`

Example: `6380` is host port and `6379` is container port.

```yaml
ports:
  - "6380:6379"
```