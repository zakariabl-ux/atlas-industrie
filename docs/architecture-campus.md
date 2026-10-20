# Decision note - architecture of the Atlas Industrie campus

## 1. Context and requirements

Atlas Industrie operates two buildings: A hosts head office and IT;
B hosts production, industrial controllers and supervision systems.
The campus inventory contains two cores, two firewalls and ten access
switches: six in A and four in B.

Production in B must continue if building A becomes isolated.
Essential OT routing, policies and services must therefore remain local
to B. Internet access and central IT services may be degraded.

The design must separate infrastructure, IT, voice, guest, OT and DMZ
traffic, provide IPv4 and IPv6 addressing, and remain affordable over
a five-year operating period.

## 2. Technical targets retained

- Adopt a distributed collapsed core: CC-A in A and CC-B in B.
- Provide two inter-building fibre links using distinct named routes.
- Use separate technical rooms and separate UPS units for the cores.
- Target 99.99% annual production availability; this is not yet proven.
- Target production recovery within 15 minutes, to be verified by tests.
- Keep essential OT routing and services operational within building B.
- Apply default deny between IT and OT; permit only documented flows.
- Place firewall filtering at trust-zone boundaries and isolate the DMZ.
- Define detailed firewall interfaces and failover before implementation.
- Use the VLAN plan in data/vlans.yml as the addressing authority.
- Keep VLAN 99 non-routed and shut down its unused ports.

## 3. The two architectures compared
S1 - Three-tier architecture:
Two modular cores, four distribution switches, ten access switches
and two firewalls: 18 managed devices.
The separate distribution tier adds capacity and flexibility, but
increases equipment cost and operational effort.

S2 - Distributed collapsed core:
Two fixed L3 cores, ten access switches and two firewalls:
14 managed devices. CC-A is in building A; CC-B is in building B.
This removes the distribution tier while retaining local routing
in B and two inter-building fibre links.
Production autonomy also requires local OT services and policies;
placing a core in B alone does not guarantee continuity.

S3 - Single-site collapsed core:
The same device count as S2, but both cores are in building A.
It costs less because the fibre allowance is lower.
However, losing building A removes both cores, so this scenario
does not meet the requirement for autonomous production in B.

The principal comparison is S1 versus S2. S3 is included as a
cost baseline to quantify the price of distributing the cores.

## 4. Five-year costing
All amounts are in MAD, excluding tax, over five years.

Price assumptions:
- Access switch: 27,000 each.
- Modular core: 162,000 each.
- Fixed core: 86,400 each.
- Distribution switch: 54,000 each.
- Firewall pair: 216,000.
- Optics and fibre: S1 129,600; S2 108,000; S3 86,400.

Calculation rules:
Annual support = 10% of CAPEX.
Annual operations = device count x 1.5 days x 5,400.
Five-year TCO = CAPEX + five-year support + five-year operations.
Device counts include both firewalls.

S1 - Three-tier architecture:
CAPEX = 2 x 162,000 + 4 x 54,000 + 10 x 27,000
        + 216,000 + 129,600 = 1,155,600.
Five-year support = 1,155,600 x 10% x 5 = 577,800.
Five-year operations = 18 x 1.5 x 5,400 x 5 = 729,000.
TCO = 1,155,600 + 577,800 + 729,000 = 2,462,400.

S2 - Distributed collapsed core:
CAPEX = 2 x 86,400 + 10 x 27,000 + 216,000
        + 108,000 = 766,800.
Five-year support = 766,800 x 10% x 5 = 383,400.
Five-year operations = 14 x 1.5 x 5,400 x 5 = 567,000.
TCO = 766,800 + 383,400 + 567,000 = 1,717,200.

S3 - Single-site collapsed core:
CAPEX = 2 x 86,400 + 10 x 27,000 + 216,000
        + 86,400 = 745,200.
Five-year support = 745,200 x 10% x 5 = 372,600.
Five-year operations = 14 x 1.5 x 5,400 x 5 = 567,000.
TCO = 745,200 + 372,600 + 567,000 = 1,684,800.

S2 saves 745,200 compared with S1 over five years.
S2 costs 32,400 more than S3 over five years.
At 108,000 per hour of production downtime:
32,400 / 108,000 = 0.3 hours = 18 minutes.
Avoiding 18 minutes of production downtime over five years
would offset S2's additional cost relative to S3.

## 5. Decision and justification
Retain S2, the distributed collapsed core, with CC-A in building A
and CC-B in building B.

Compared with S1, S2 saves 745,200 MAD over five years and reduces
the managed device count from 18 to 14. A separate distribution
tier is not justified for the stated campus scope. Port capacity,
traffic demand and future growth must still be verified.

S3 is cheaper, but it fails a stated requirement: production in B
must continue when building A is isolated. Both cores in A would
be lost together during an A-site failure.

S2's additional five-year cost over S3 is 32,400 MAD, equivalent
to 18 minutes of production downtime at 108,000 MAD per hour.
The premium is economically justified if it avoids at least
18 minutes of downtime over five years. This is a break-even
threshold, not a prediction of outage frequency.

Production continuity requires local OT routing, essential services
and security policies in B. IT-to-OT traffic is denied by default;
only explicitly approved flows are permitted through firewall
filtering. DMZ services remain in a separate filtered zone.

The selection does not demonstrate 99.99% availability. Fibre-route
risks, access switches, power and failover behaviour require further
analysis and testing.

## 6. Risks and points of failure
Assuming each device has 99.9% availability, three independent
devices in series give:
0.999 x 0.999 x 0.999 = 0.997002999, or about 99.7003%.

An independent redundant pair gives:
1 - (1 - 0.999)^2 = 0.999999, or 99.9999%.

With redundant core and firewall pairs but one non-redundant
access switch, the simplified path availability is:
0.999999 x 0.999999 x 0.999 = 0.998998002001,
or about 99.8998%, still below the 99.99% target.
This illustration assumes effective failover and is not proof
that the proposed topology achieves those figures.

The physical view identifies a remaining common cause: both fibre
routes pass through the courtyard cable chamber. Excavation there
could cut both links despite their otherwise distinct routes.

Separate UPS units reduce dependence on one UPS, but upstream
electrical supply independence has not been established.
Shared software defects, configuration errors and maintenance
mistakes can also affect redundant devices together.

A local access-switch failure can disconnect attached production
equipment. Critical endpoints, power supplies, local OT services,
firewall placement and failover mechanisms need further analysis.

Before implementation, verify building B's operation while A is
isolated and measure recovery time during controlled failures.

## 7. Assumptions and limits
The five-year estimate uses the lab's teaching prices in MAD,
excluding tax, at the reference conversion of 1 EUR = 10.8 MAD.
These estimates require vendor quotations before procurement.

Support is 10% of CAPEX per year. Operations require 1.5 days per
managed device per year at 5,400 MAD per day. Both firewalls are
included in every scenario's device count.

Power, cooling, civil works, endpoint network cards and any
additional licensing or service-replication costs are excluded.
UPS units are shown in the physical design but are not separately
priced in the provided equipment assumptions.

The named fibre routes, shared courtyard chamber and separate UPS
units are proposed assumptions, not verified site observations.

The logical view is adapted from the chapter's distributed-core
architecture and the inventory. Access connections and firewall
placement are assumptions; it is not an exact copy of the lab's
unavailable detailed reference diagram.

Essential OT services are assumed to be available locally in B.
Their dependencies and any additional costs must be established.
Traffic demand, link capacity and growth have not been measured.

The availability calculation assumes independent failures and
perfect failover. Real common causes weaken that assumption.
Neither the 99.99% target nor the 15-minute recovery target has
been demonstrated by implementation or testing.

