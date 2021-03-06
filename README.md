# locksmithd

locksmithd is a reboot manager for the CoreOS update engine which uses
etcd to ensure that only a subset of a cluster of machines is rebooting
at any given time.

## Usage

### Listing the Holders

```
$ locksmithctl status
Available: 0
Max: 1

MACHINE ID
69d27b356a94476da859461d3a3bc6fd
```

### Unlock Holders

In some cases a machine may go away permanently or semi-permanently while
holding a reboot lock. A system administrator can clear this lock using the
unlock command.

```
$ locksmithctl unlock 69d27b356a94476da859461d3a3bc6fd
```

### Maximum Sempahore

By default the reboot lock only allows a single holder. However, a user may
want more than a single machine to be upgrading at a time. This can be done by
increasing the semaphore count.

```
$ locksmithctl set-max 4
Old: 1
New: 4
```

## Keyspace

### Semaphore

Key: `coreos.com/updateengine/rebootlock/semaphore`

The semaphore is a json document that describes a simple semaphore that clients
swap to take the lock. When it is first created it will be initialized like so:

```json
{
	"semaphore": 1,
	"max": 1,
	"holders": []
}
```

To take the lock a client the document will be swaped with this:

```json
{
	"semaphore": 0,
	"max": 1,
	"holders": [
		"69d27b356a94476da859461d3a3bc6fd"
	]
}
```
