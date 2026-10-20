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

## Automatic validation
After cloning, install Python 3 and run:
python -m pip install pyyaml

Enable the versioned pre-commit hook:
git config core.hooksPath hooks

The hook runs the IP plan check before each commit and blocks
the commit if the check fails.
On Linux or macOS, also run: chmod +x hooks/pre-commit
