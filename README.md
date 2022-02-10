zincrsend - ZFS Incremental Send
================================

Incremental ZFS send/recv backup script

Configuration
-------------
Modify configuration options to fit your environment.

``` bash
################################################################################
# Configuration options
################################################################################
# Recursive datasets to send. (-R) will remove snapshots that have been deleted
# locally on the remote end as well. Datasets do *NOT* need to have children. 
datasets=(
  tank/example_dataset
)

# Remote server connection settings.
remote_server='192.168.0.10'
remote_user='zfs_snapshot'
remote_port='22'
remote_pool='backup'
remote_command_prefix='sudo'
remote_ssh_opts=(-i /etc/zfs_snapshot/.ssh/id_rsa)

# prefix to use for snapshots created by this script
snapshot_prefix=''
# Number of snapshots to retain after successful sync. 0 disables.
# If running as a non-root user, be sure to add user with mount,destroy
# privileges on the dataset. This will allow snapshot management.
#
#  /usr/sbin/zfs allow -d {ZFS USER} mount,destroy tank/example_dataset
#
snapshot_retention=1
# Snapshot options.
# Reference:
# * https://openzfs.github.io/openzfs-docs/man/8/zfs-snapshot.8.html
snapshot_opts=(-r)
# Send options.
# Reference:
# * https://openzfs.github.io/openzfs-docs/man/8/zfs-send.8.html
send_opts=(-R -w)
################################################################################
```

Example
-------

```
$ ./zincrsend
$ starting on Wed 09 Feb 2022 05:11:42 PM PST

processing dataset: hundo/home

creating snapshot locally: hundo/home@1644455502
fetching latest remote snapshot for dataset: tens/home
latest remote snapshot: tens/home@1644443955
zfs sending (incremental) @1644443955 -> hundo/home@1644455502 to tens/home
ssh -i /etc/zfs_snapshot/.ssh/id_rsa 192.168.0.10 sudo /usr/sbin/zfs recv -Fuv tens/home
zfs_snapshot@pm1:~$ attempting destroy tens/home@1644441006
success
receiving incremental stream of hundo/home@1644455502 into tens/home@1644455502
received 7.73M stream in 1 seconds (7.73M/sec)
retainng the last 1 snapshots for hundo/home
all snapshots:       hundo/home@1644455502 hundo/home@1644443955
retained snapshots:  hundo/home@1644455502
snapshots to remove: hundo/home@1644443955

processing dataset: hundo/documents

creating snapshot locally: hundo/documents@1644455511
fetching latest remote snapshot for dataset: tens/documents
latest remote snapshot: tens/documents@1644443961
zfs sending (incremental) @1644443961 -> hundo/documents@1644455511 to tens/documents
ssh -i /etc/zfs_snapshot/.ssh/id_rsa 192.168.0.10 sudo /usr/sbin/zfs recv -Fuv tens/documents
attempting destroy tens/documents@1644156935
success
attempting destroy tens/documents@1644184672
success
attempting destroy tens/documents@1644441015
success
receiving incremental stream of hundo/documents@1644455511 into tens/documents@1644455511
received 15.7K stream in 1 seconds (15.7K/sec)
retainng the last 1 snapshots for hundo/documents
all snapshots:       hundo/documents@1644455511 hundo/documents@1644443961
retained snapshots:  hundo/documents@1644455511
snapshots to remove: hundo/documents@1644443961

processing dataset: hundo/music

creating snapshot locally: hundo/music@1644470197
fetching latest remote snapshot for dataset: tens/music
latest remote snapshot: tens/music@1644443978
zfs sending (incremental) @1644443978 -> hundo/music@1644470197 to tens/music
ssh -i /etc/zfs_snapshot/.ssh/id_rsa 192.168.0.10 sudo /usr/sbin/zfs recv -Fuv tens/music
attempting destroy tens/music@1644441036
success
receiving incremental stream of hundo/music@1644470197 into tens/music@1644470197
received 114G stream in 8837 seconds (13.2M/sec)
retainng the last 1 snapshots for hundo/music
all snapshots:       hundo/music@1644470197 hundo/music@1644443978
retained snapshots:  hundo/music@1644470197
snapshots to remove: hundo/music@1644443978

script ran for ~147 minutes (8839 seconds)
```

License
-------

MIT License

