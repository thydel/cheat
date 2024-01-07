<!--
< README.md pandoc -f gfm -t gfm --toc --template ~/toc.md --columns=132
-->

-   [JQ](#jq)
    -   [Examines skeleton of big data using stream](#examines-skeleton-of-big-data-using-stream)
    -   [Lib](#lib)
        -   [Keeps first elt of all arrays](#keeps-first-elt-of-all-arrays)
        -   [Keeps type instead of value](#keeps-type-instead-of-value)
        -   [Shows as path](#shows-as-path)
-   [Mate and CRD on debian 12](#mate-and-crd-on-debian-12)
-   [Chrome and CRD on debian 12](#chrome-and-crd-on-debian-12)

# JQ

## Examines skeleton of big data using stream

- e.g. from

```bash
govc find -type m | xargs govc vm.info -json > vms.json
```

## Lib

```jq
def firstElt(f): fromstream(inputs | select(first | map(numbers == 0) | all) | f);
def firstElt: firstElt(.);
def firstEltType: firstElt(if length == 2 then last |= type else . end);
def asPath: tostream | select(length == 2) | first + [last] | join("/");
```

### Keeps first elt of all arrays

```jq
< vms.json jq -n --stream 'include "lib"; firstElt'
```

### Keeps type instead of value

```jq
< vms.json jq -n --stream 'include "lib"; firstEltType'
```

### Shows as path

```jq
< vms.json jq -nr --stream 'include "lib"; firstEltType | asPath'
```

# Mate and CRD on debian 12

- See [Could not acquire name on session bus - SOLVED][]
  and [Bug 1350004 - simultaneous x2go and desktop sessions cause dbus conflicts][]

```bash
r () { declare -f $1; echo $@; }
f () { sed -i.$(date +%F) '/^fi/i \    unset DBUS_SESSION_BUS_ADDRESS' /etc/X11/Xsession.d/99mate-environment; }
r f | ssh $node -l root bash
```

[Could not acquire name on session bus - SOLVED]:
    https://community.thinlinc.com/t/could-not-acquire-name-on-session-bus-solved/132
    "community.thinlinc.com"

[Bug 1350004 - simultaneous x2go and desktop sessions cause dbus conflicts]:
	https://bugzilla.redhat.com/show_bug.cgi?id=1350004
    "bugzilla.redhat.com"

# Chrome and CRD on debian 12

- See [Overriding the User Data Directory][]

        Chrome Remote Desktop (CRD) used to set $CHROME_USER_DATA_DIR or
        $CHROME_CONFIG_HOME on the virtual session on a Linux host, since
        a single Chrome instance cannot show windows on multiple X
        displays, and two running Chrome instances cannot share the same
        user data directory. However, with the obsolescence of dbus-x11,
        most modern Linux distros have lost the ability to simultaneously
        run multiple graphical sessions for the same user without running
        into difficult-to-trace dbus cross talk issues, and Chrome can
        only be run on a single X display per user in reality. Therefore,
        CRD no longer sets these environment variables for new
        installations after CRD host M105.

```bash
google-chome --user-data-dir ~/.config/google-chrome-RD
```

[Overriding the User Data Directory]:
    https://chromium.googlesource.com/chromium/src/+/HEAD/docs/user_data_dir.md#Overriding-the-User-Data-Directory
    "chromium.googlesource.com"

[Local Variables:]::
[indent-tabs-mode: nil]::
[End:]::
