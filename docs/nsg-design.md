# Network Security Group (NSG) Design

## Overview
This document describes the NSG rules applied to each subnet in the Azure Network Foundation project.
The goal is to implement a least-privilege security model while allowing required communication between layers.

---

## WebSubnet NSG

### Inbound Rules
| Priority | Source | Port | Protocol | Action | Description |
|--------|--------|------|----------|--------|------------|
| 110 | ManagementSubnet | 22,3389 | TCP | Allow | Administrative access |
| 120 | AppSubnet | Any | Any | Allow | Application communication |
| 65500 | Any | Any | Any | Deny | Default deny |

### Outbound Rules
| Priority | Destination | Port | Protocol | Action | Description |
|--------|-------------|------|----------|--------|------------|
| 100 | AppSubnet | Any | Any | Allow | Web to App |
| 110 | Internet | 80,443 | TCP | Allow | Updates via NAT |
| 65500 | Any | Any | Any | Deny | Default deny |

---

## AppSubnet NSG

### Inbound Rules
| Priority | Source | Port | Protocol | Action | Description |
|--------|--------|------|----------|--------|------------|
| 100 | WebSubnet | Any | Any | Allow | Web to App |
| 110 | ManagementSubnet | 22,3389 | TCP | Allow | Administrative access |
| 65500 | Any | Any | Any | Deny | Default deny |

### Outbound Rules
| Priority | Destination | Port | Protocol | Action | Description |
|--------|-------------|------|----------|--------|------------|
| 100 | DataSubnet | Any | Any | Allow | App to Data |
| 110 | Internet | 80,443 | TCP | Allow | Updates via NAT |
| 65500 | Any | Any | Any | Deny | Default deny |

---

## DataSubnet NSG

### Inbound Rules
| Priority | Source | Port | Protocol | Action | Description |
|--------|--------|------|----------|--------|------------|
| 100 | AppSubnet | 1433 / 3306 | TCP | Allow | App to Database |
| 110 | ManagementSubnet | 22 | TCP | Allow | Administrative access (if required) |
| 65500 | Any | Any | Any | Deny | Default deny |

### Outbound Rules
| Priority | Destination | Port | Protocol | Action | Description |
|--------|-------------|------|----------|--------|------------|
| 100 | Internet | 80,443 | TCP | Allow | OS updates via NAT |
| 65500 | Any | Any | Any | Deny | Default deny |

---

## ManagementSubnet NSG

### Inbound Rules
| Priority | Source | Port | Protocol | Action | Description |
|--------|--------|------|----------|--------|------------|
| 100 | AzureBastionSubnet | 22,3389 | TCP | Allow | Bastion access |
| 65500 | Any | Any | Any | Deny | Default deny |

### Outbound Rules
| Priority | Destination | Port | Protocol | Action | Description |
|--------|-------------|------|----------|--------|------------|
| 100 | WebSubnet | Any | Any | Allow | Administrative tasks |
| 110 | AppSubnet | Any | Any | Allow | Administrative tasks |
| 120 | DataSubnet | Any | Any | Allow | Administrative tasks |
| 65500 | Any | Any | Any | Deny | Default deny |
