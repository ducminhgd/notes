# Signals (The GNU Library)

## [[SIGKILL]]

The SIGKILL is used for immediate termination of a process. This signal cannot be ignored or blocked. The process will be terminated along with its threads (if any).

It is the brutal way of killing a process and it should only be used as the last resort. Suppose you have an unresponsive process that you want to close. The SIGKILL can be used in such a case.

```bash
kill -9 <process_id>
```

## [[SIGTERM]]

In [[UNIX-like]] systems, the SIGTERM signal is used for terminating a program. You can pretty much guess that from its name which is made up of SIGnal and TERMinate.

The SIGTERM can also be referred as soft kill because the process that receives the SIGTERM signal may choose to ignore it.

In other words, it's the polite way of killing a process.

```bash
kill <process_id>
```