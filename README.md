# puppet-hiera-regex

This code is split out from wmflib ( https://phabricator.wikimedia.org/source/operations-puppet/browse/production/modules/wmflib/ )
With small adjustment to the the module, I got it working in Puppet 7 Community edition

## hiera.yaml examples
```
  - name: "Per-node - regex based settings"
    lookup_key: regex_data
    path: "nodes/regex.yaml" # regex
    options:
      node: "%{::trusted.certname}"
```

```
  - name: "common - regex based settings"
    lookup_key: regex_data
    path: "common/regex/*.yaml" # regex
    options:
      node: "%{::trusted.certname}"
```

## data/nodes/regex.yaml:
```
---
# regex file to match servernames and put them to different patching_as_code::patch_group group based on their name
patching_as_code_based_on_hostname_stage:
    __regex: !ruby/regexp /^.*stage.*$/
    patching_as_code::patch_group: ['StageTime']

patching_as_code_based_on_hostname_even:
    __regex: !ruby/regexp /^.*[01]*[2468].*$/
    patching_as_code::patch_group: ['EvenTime']

patching_as_code_based_on_hostname_odd:
    __regex: !ruby/regexp /^.*[01]*[13579].*$/
    patching_as_code::patch_group: ['OddTime']

hostnamematching:
    __regex: !ruby/regexp /server.fullname.org*/
    patching_as_code::patch_group: ['MatchedHostname']
```
