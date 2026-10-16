# Multilayer Switching — Inter-VLAN Routing

## Overview

This lab demonstrates Inter-VLAN Routing using a Cisco Multilayer
Switch (Layer 3 switch).

Unlike Router-on-a-Stick, the routing between VLANs is performed
directly by the multilayer switch using Switched Virtual Interfaces
(SVIs).

The multilayer switch also provides connectivity toward a simulated
Internet through an external router.

## Main Technologies

- VLANs
- Layer 3 switching
- SVIs
- Inter-VLAN Routing
- `ip routing`
- Default gateway
- Static routing
- Internet edge connectivity

## Architecture

LAN → Multilayer Switch → Edge Router → Simulated Internet

## Repository Structure

```text
.
├── configs/
│   └── Device configuration files (.txt)
├── docs/
│   ├── documentation.md
│   └── topology.png
├── src/
│   └── VTP-DTP-Lab.pkt
├── tests/
│   ├── simulation-mode.png
│   ├── end-to-end-connectivity.png
│   ├── shows.txt
│   └── Additional verification screenshots
├── .gitignore
├── LICENSE
└── README.md
```

## Author

**C. Victorin Vodinou**

_Networking_ | _Systems Administration_ | _Network Security_

This project is part of my hands-on networking lab portfolio.
