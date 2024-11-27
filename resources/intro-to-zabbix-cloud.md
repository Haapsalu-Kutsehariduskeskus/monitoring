# Understanding Zabbix Cloud

## What is Zabbix Cloud?
Zabbix Cloud is a SaaS (Software as a Service) version of Zabbix monitoring software. Instead of managing your own Zabbix server, database, and infrastructure, Zabbix handles everything in the cloud while you focus on monitoring.

![Zabbix Cloud Architecture](https://www.whatsuphome.fi/sites/default/files/styles/wide/public/2024-10/Screenshot%202024-10-01%20at%2015.18.02.png?itok=qSDeWVTo)

## Key Features

### Managed Infrastructure
- No need to manage servers, databases, or updates
- Automatic security patches and upgrades
- Built-in high availability
- Professional maintenance by Zabbix team

### Pricing Model
Based on two factors:
1. NVPS (New Values Per Second)
2. Storage capacity

Starting from $50/month for:
- 50 NVPS
- 10GB free storage
- Additional storage: $0.30/GB

### Available Regions
- Europe: Frankfurt, Ireland
- Americas: North Virginia, São Paulo
- Asia: Singapore

## Service Tiers

| Tier | NVPS | Recommended Storage |
|------|------|-------------------|
| Nano | 50 | 10GB |
| Micro | 100 | 25GB |
| Small | 250 | 100GB |
| Medium | 1000 | 250GB |
| Large | 2500 | 500GB |
| XLarge | 5000 | 1TB |
| 2XLarge | 10000 | 2TB |

## Platform Limitations

Current restrictions for security:
- No remote commands on Zabbix server
- No custom alert scripts
- No SNMP traps on server (use proxy instead)
- No web authentication for frontend
- Limited ODBC support
- No external scripts

## Security Features
- Complete customer isolation
- Static IPv4 addresses
- Encrypted communication
- Regular backups
- Weekly automated backups included
- Manual backup options available

## Platform Management
- Maintenance windows required (minimum 1 hour weekly)
- Automated version upgrades
- Security patches
- Performance optimization
- Database tuning

## Getting Started
1. Register at cloud.zabbix.com
2. Choose region and tier
3. Deploy your node
4. Configure monitoring

5-day free trial available to test all features.

## Ideal Use Cases
- Companies without dedicated monitoring infrastructure
- Organizations seeking managed monitoring solutions
- MSPs providing monitoring services
- Distributed monitoring requirements

## Upcoming Features
- Multiple user support
- Enhanced backup options
- Proxy deployment in cloud
- API improvements
- Additional regions

Remember: While Zabbix Cloud removes infrastructure management overhead, it maintains all core monitoring capabilities of traditional Zabbix installations.