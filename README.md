# puppet-hiera-regex
I got it working in Puppet 7 Community edition. This module will use regex in a hiera file to match your configured parameter in hiera. Example used below is %{::trusted.certname}. This could be anything.

This project splits out wmflib where it is included as a part of a bigger puppet setup for wikimedia, I found it usefull to extract it and reuse it as a smaller module.
See https://phabricator.wikimedia.org/source/operations-puppet/browse/production/modules/wmflib/ for the full version of their module.

After looking into https://github.com/jjulien/hiera-regex
I found out that jjulien's module was not working for the new Hiera and how it now uses different backends. I knew I needed something a bit better included in the hiera to allow for regex.
With small adjustment to the the module I got this working perfectly to match hostnames based on regex.

# requirements:
- Puppet 7
- Hiera
- Puppetfile w/r10k

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
