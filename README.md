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
