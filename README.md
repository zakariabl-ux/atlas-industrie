# Atlas Industrie - network architecture repository

Design of the Atlas Industrie network, the fictitious company of the
"Advanced Network Architectures" course. One folder per object type;
the addressing plan is authoritative in `data/vlans.yml`.

Local check: `python scripts/check_vlans.py` from the repository root.
IPv6 ULA prefix: `fd49:4a21:d2ec::/48`.


## Validation scope
The script checks duplicate VLAN IDs and overlapping IPv4 networks.
It does not validate IPv6 prefixes, mandatory fields, or whether each
subnet belongs to its assigned zone block.
