

## CRIU -- A project to implement checkpoint/restore functionality for Linux

CRIU (stands for Checkpoint and Restore in Userspace) is a utility to checkpoint/restore Linux tasks.

Using this tool, you can freeze a running application (or part of it) and checkpoint
it to a hard drive as a collection of files. You can then use the files to restore and run the
application from the point it was frozen at. The distinctive feature of the CRIU
project is that it is mainly implemented in user space. There are some more projects
doing C/R for Linux, and so far CRIU [appears to be](https://criu.org/Comparison_to_other_CR_projects)
the most feature-rich and up-to-date with the kernel.

CRIU project is (almost) the never-ending story, because we have to always keep up with the
Linux kernel supporting checkpoint and restore for all the features it provides. Thus we're
looking for contributors of all kinds -- feedback, bug reports, testing, coding, writing, etc.
Please refer to [CONTRIBUTING.md](CONTRIBUTING.md) if you would like to get involved.

The project [started](https://criu.org/History) as the way to do live migration for OpenVZ
Linux containers, but later grew to more sophisticated and flexible tool. It is currently
used by (integrated into) OpenVZ, LXC/LXD, Docker, and other software, project gets tremendous
help from the community, and its packages are included into many Linux distributions.






The project home is at http://criu.org. This wiki contains all the knowledge base for CRIU we have.
Pages worth starting with are:
- [Installation instructions](http://criu.org/Installation)
- [A simple example of usage](http://criu.org/Simple_loop)
- [Examples of more advanced usage](https://criu.org/Category:HOWTO)
- Troubleshooting can be hard, some help can be found [here](https://criu.org/When_C/R_fails), [here](https://criu.org/What_cannot_be_checkpointed) and [here](https://criu.org/FAQ)



## Installation 

Change python to python3
```
python2 --version
python3 --version

ls /usr/bin/python*
sudo update-alternatives --list python


sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 2

sudo update-alternatives --config python
python --version

```

Install
```
apt --fix-broken install
apt install protobuf-compiler libc6 libgnutls30 libnftables1  
apt-get install  protobuf-c-compiler
apt-get install libprotobuf-dev libprotobuf-c-dev protobuf-c-compiler protobuf-compiler python3-protobuf
apt-get install pkg-config python-ipaddress libbsd-dev iproute2  libnftables-dev libcap-dev   libnl-3-dev libnet-dev
apt-get install libaio-dev libgnutls28-dev python3-future 
apt-get install asciidoc xmlto
apt-get install python3-distutils



make
make install
criu check


make docker-build


```
To use

```
root@n223-247-006:~/criu# pwd
/root/criu
root@n223-247-006:~/criu# /root/criu/criu/criu 

Usage:
  criu dump|pre-dump -t PID [<options>]
  criu restore [<options>]
  criu check [--feature FEAT]
  criu page-server
  criu service [<options>]
  criu dedup
  criu lazy-pages -D DIR [<options>]

Commands:
  dump           checkpoint a process/tree identified by pid
  pre-dump       pre-dump task(s) minimizing their frozen time
  restore        restore a process/tree
  check          checks whether the kernel support is up-to-date
  page-server    launch page server
  service        launch service
  dedup          remove duplicates in memory dump
  cpuinfo dump   writes cpu information into image file
  cpuinfo check  validates cpu information read from image file

Try -h|--help for more info
```

## CRIU Usage 

```
criu dump -D ./ -t 1234 --shell-jobs
criu restore -d -D ./ --shell-jobs

```


```
root@n223-247-006:~/test-runc-nginx# criu -h

Usage:
  criu dump|pre-dump -t PID [<options>]
  criu restore [<options>]
  criu check [--feature FEAT]
  criu page-server
  criu service [<options>]
  criu dedup
  criu lazy-pages -D DIR [<options>]

Commands:
  dump           checkpoint a process/tree identified by pid
  pre-dump       pre-dump task(s) minimizing their frozen time
  restore        restore a process/tree
  check          checks whether the kernel support is up-to-date
  page-server    launch page server
  service        launch service
  dedup          remove duplicates in memory dump
  cpuinfo dump   writes cpu information into image file
  cpuinfo check  validates cpu information read from image file

Most of the true / false long options (the ones without arguments) can be
prefixed with --no- to negate the option (example: --display-stats and
--no-display-stats).

Dump/Restore options:

* Generic:
  -t|--tree PID         checkpoint a process tree identified by PID
  -d|--restore-detached detach after restore
  -S|--restore-sibling  restore root task as sibling
  -s|--leave-stopped    leave tasks in stopped state after checkpoint
  -R|--leave-running    leave tasks in running state after checkpoint
  -D|--images-dir DIR   directory for image files
     --pidfile FILE     write root task, service or page-server pid to FILE
  -W|--work-dir DIR     directory to cd and write logs/pidfiles/stats to
                        (if not specified, value of --images-dir is used)
     --cpu-cap [CAP]    CPU capabilities to write/check. CAP is comma-separated
                        list of: cpu, fpu, all, ins, none. To disable
                        a capability, use ^CAP. Empty argument implies all
     --exec-cmd         execute the command specified after '--' on successful
                        restore making it the parent of the restored process
  --freeze-cgroup       use cgroup freezer to collect processes
  --weak-sysctls        skip restoring sysctls that are not available
  --lazy-pages          restore pages on demand
                        this requires running a second instance of criu
                        in lazy-pages mode: 'criu lazy-pages -D DIR'
                        --lazy-pages and lazy-pages mode require userfaultfd
  --stream              dump/restore images using criu-image-streamer
  --mntns-compat-mode   Use mount engine in compatibility mode. By default criu
                        tries to use mount-v2 mode with more reliable algorithm
                        based on MOVE_MOUNT_SET_GROUP kernel feature
  --network-lock METHOD
                      network locking/unlocking method; argument
                      can be 'nftables' or 'iptables' (default).

* External resources support:
  --external RES        dump objects from this list as external resources:
                        Formats of RES on dump:
                            tty[rdev:dev]
                            file[mnt_id:inode]
                            dev[major/minor]:NAME
                            unix[ino]
                            mnt[MOUNTPOINT]:COOKIE
                            mnt[]{:AUTO_OPTIONS}
                        Formats of RES on restore:
                            dev[NAME]:DEVPATH
                            veth[IFNAME]:OUTNAME{@BRIDGE}
                            macvlan[IFNAME]:OUTNAME
                            mnt[COOKIE]:ROOT
                            netdev[IFNAME]:ORIGNAME

* Special resources support:
     --tcp-established  checkpoint/restore established TCP connections
     --skip-in-flight   skip (ignore) in-flight TCP connections
     --tcp-close        don't dump the state of, or block, established tcp
                        connections, and restore them in closed state.
  -r|--root PATH        change the root filesystem (when run in mount namespace)
  --evasive-devices     use any path to a device file if the original one
                        is inaccessible
  --link-remap          allow one to link unlinked files back when possible
  --ghost-limit size    limit max size of deleted file contents inside image
  --action-script FILE  add an external action script
  -j|--shell-job        allow one to dump and restore shell jobs
  -l|--file-locks       handle file locks, for safety, only used for container
  -L|--libdir           path to a plugin directory (by default /usr/lib/criu/)
  --timeout NUM         a timeout (in seconds) on collecting tasks during dump
                        (default 10 seconds)
  --force-irmap         force resolving names for inotify/fsnotify watches
  --irmap-scan-path FILE
                        add a path the irmap hints to scan
  --manage-cgroups [m]  dump/restore process' cgroups; argument can be one of
                        'none', 'props', 'soft' (default), 'full', 'strict'
                        or 'ignore'
  --cgroup-root [controller:]/newroot
                        on dump: change the root for the controller that will
                        be dumped. By default, only the paths with tasks in
                        them and below will be dumped.
                        on restore: change the root cgroup the controller will
                        be installed into. No controller means that root is the
                        default for all controllers not specified
  --cgroup-props STRING
                        define cgroup controllers and properties
                        to be checkpointed, which are described
                        via STRING using simplified YAML format
  --cgroup-props-file FILE
                        same as --cgroup-props, but taking description
                        from the path specified
  --cgroup-dump-controller NAME
                        define cgroup controller to be dumped
                        and skip anything else present in system
  --cgroup-yard PATH
                        instead of trying to mount cgroups in CRIU, provide
                        a path to a directory with already created cgroup yard.
                        Useful if you don't want to grant CAP_SYS_ADMIN to CRIU
  --lsm-profile TYPE:NAME
                        Specify an LSM profile to be used during restore.
                        The type can be either 'apparmor' or 'selinux'.
  --lsm-mount-context CTX
                        Specify a mount context to be used during restore.
                        Only mounts with an existing context will have their
                        mount context replaced with CTX.
  --skip-mnt PATH       ignore this mountpoint when dumping the mount namespace
  --enable-fs FSNAMES   a comma separated list of filesystem names or "all"
                        force criu to (try to) dump/restore these filesystem's
                        mountpoints even if fs is not supported
  --inherit-fd fd[NUM]:RES
                        Inherit file descriptors, treating fd NUM as being
                        already opened via an existing RES, which can be:
                            tty[rdev:dev]
                            pipe[inode]
                            socket[inode]
                            file[mnt_id:inode]
                            /memfd:name
                            path/to/file
  --empty-ns net        Create a namespace, but don't restore its properties
                        (assuming it will be restored by action scripts)
  -J|--join-ns NS:{PID|NS_FILE}[,OPTIONS]
			Join existing namespace and restore process in it.
			Namespace can be specified as either pid or file path.
			OPTIONS can be used to specify parameters for userns:
			    user:PID,UID,GID
  --file-validation METHOD
			pass the validation method to be used; argument
			can be 'filesize' or 'buildid' (default).

Check options:
  Without options, "criu check" checks availability of absolutely required
  kernel features, critical for performing dump and restore.
  --extra               add check for extra kernel features
  --experimental        add check for experimental kernel features
  --all                 same as --extra --experimental
  --feature FEAT        only check a particular feature, one of:
                            mnt_id, mem_dirty_track, aio_remap, timerfd, tun, 
                            tun_ns, userns, fdinfo_lock, seccomp_suspend, 
                            seccomp_filters, loginuid, cgroupns, autofs, 
                            tcp_half_closed, compat_cr, uffd, uffd-noncoop, 
                            can_map_vdso, sk_ns, sk_unix_file, net_diag_raw, 
                            nsid, link_nsid, kcmp_epoll, timens, 
                            external_net_ns, clone3_set_tid, newifindex, 
                            nftables, has_ipt_legacy, pidfd_store, ns_pid, 
                            apparmor_stacking, network_lock_nftables, 
                            sockopt_buf_lock, memfd_hugetlb, 
                            move_mount_set_group, openat2, get_rseq_conf

* Logging:
  -o|--log-file FILE    log file name
     --log-pid          enable per-process logging to separate FILE.pid files
  -v[v...]|--verbosity  increase verbosity (can use multiple v)
  -vNUM|--verbosity=NUM set verbosity to NUM (higher level means more output):
                          -v1 - only errors and messages
                          -v2 - also warnings (default level)
                          -v3 - also information messages and timestamps
                          -v4 - lots of debug
  --display-stats       print out dump/restore stats

* Memory dumping options:
  --track-mem           turn on memory changes tracker in kernel
  --prev-images-dir DIR path to images from previous dump (relative to -D)
  --page-server         send pages to page server (see options below as well)
  --auto-dedup          when used on dump it will deduplicate "old" data in
                        pages images of previous dump
                        when used on restore, as soon as page is restored, it
                        will be punched from the image
  --pre-dump-mode       splice - parasite based pre-dumping (default)
                        read   - process_vm_readv syscall based pre-dumping

Page/Service server options:
  --address ADDR        address of server or service
  --port PORT           port of page server
  --ps-socket FD        use specified FD as page server socket
  -d|--daemon           run in the background after creating socket
  --status-fd FD        write \0 to the FD and close it once process is ready
                        to handle requests
  --tls-cacert FILE     trust certificates signed only by this CA
  --tls-cacrl FILE      path to CA certificate revocation list file
  --tls-cert FILE       path to TLS certificate file
  --tls-key FILE        path to TLS private key file
  --tls                 use TLS to secure remote connection
  --tls-no-cn-verify    do not verify common name in server certificate

Configuration file options:
  --config FILEPATH     pass a specific configuration file
  --no-default-config   forbid usage of default configuration files

Other options:
  -h|--help             show this text
  -V|--version          show version

```

## CRIT Usage
```
root@n223-247-006:~/test-runc-nginx# crit -h
usage: crit [-h] {decode,encode,info,x,show} ...

CRiu Image Tool

positional arguments:
  {decode,encode,info,x,show}
                        Use crit CMD --help for command-specific help
    decode              convert criu image from binary type to json
    encode              convert criu image from json type to binary
    info                show info about image
    x                   explore image dir
    show                convert criu image from binary to human-readable json

optional arguments:
  -h, --help            show this help message and exit

```


## Advanced features

As main usage for CRIU is live migration, there's a library for it called P.Haul. Also the
project exposes two cool core features as standalone libraries. These are libcompel for parasite code
injection and libsoccr for TCP connections checkpoint-restore.

### Live migration

True [live migration](https://criu.org/Live_migration) using CRIU is possible, but doing
all the steps by hands might be complicated. The [phaul sub-project](https://criu.org/P.Haul)
provides a Go library that encapsulates most of the complexity. This library and the Go bindings
for CRIU are stored in the [go-criu](https://github.com/checkpoint-restore/go-criu) repository.


### Parasite code injection

In order to get state of the running process CRIU needs to make this process execute
some code, that would fetch the required information. To make this happen without
killing the application itself, CRIU uses the [parasite code injection](https://criu.org/Parasite_code)
technique, which is also available as a standalone library called [libcompel](https://criu.org/Compel).

### TCP sockets checkpoint-restore

One of the CRIU features is the ability to save and restore state of a TCP socket
without breaking the connection. This functionality is considered to be useful by
itself, and we have it available as the [libsoccr library](https://criu.org/Libsoccr).

