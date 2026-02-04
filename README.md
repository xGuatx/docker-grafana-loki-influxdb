# Grafana + Loki + InfluxDB Monitoring Stack

Complete monitoring and logging stack using Grafana, Loki, and InfluxDB deployed with Docker Compose.

## Description

Integrated monitoring solution combining:
- **Grafana**: Visualization and dashboards
- **Loki**: Log aggregation
- **InfluxDB**: Time-series metrics database

## Prerequisites

- Docker
- Docker Compose

## Installation

```bash
# Start the stack
docker-compose up -d
```

## Architecture

```
+-------------+     +-------------+     +-------------+
|   Grafana   |---->|    Loki     |     |  InfluxDB   |
|   :3000     |     |   :3100     |     |   :8086     |
+-------------+     +-------------+     +-------------+
       |                    |                    |
       +--------------------+--------------------+
                    Data Sources
```

## Usage

### Access Points

- **Grafana**: http://localhost:3000
  - Default login: `admin / admin`
- **Loki**: http://localhost:3100
- **InfluxDB**: http://localhost:8086

### Initial Setup

1. **Access Grafana**
```
URL: http://localhost:3000
User: admin
Pass: admin (change on first login)
```

2. **Add Loki Data Source**
- Settings -> Data Sources -> Add data source
- Select "Loki"
- URL: `http://loki:3100`
- Save & Test

3. **Add InfluxDB Data Source**
- Settings -> Data Sources -> Add data source
- Select "InfluxDB"
- URL: `http://influxdb:8086`
- Configure database, user, password
- Save & Test

## Configuration

### Environment Variables

Create `.env` file:
```env
# Grafana
GF_SECURITY_ADMIN_PASSWORD=your_secure_password

# InfluxDB
INFLUXDB_DB=metrics
INFLUXDB_ADMIN_USER=admin
INFLUXDB_ADMIN_PASSWORD=your_secure_password
```

### Persistent Data

Data is stored in Docker volumes:
- `grafana_data` - Dashboards and config
- `loki_data` - Log data
- `influxdb_data` - Metrics data

## Features

### Grafana
- Custom dashboards
- Alerting
- User management
- Plugin support

### Loki
- Log aggregation
- LogQL queries
- Label-based indexing
- Low resource usage

### InfluxDB
- High-performance time-series DB
- InfluxQL and Flux queries
- Retention policies
- Continuous queries

## Example Queries

### Loki (LogQL)
```logql
# Show all logs
{job="varlogs"}

# Filter by level
{job="varlogs"} |= "error"

# Count errors per minute
rate({job="varlogs"} |= "error" [1m])
```

### InfluxDB (InfluxQL)
```sql
-- Show all measurements
SHOW MEASUREMENTS

-- Query CPU usage
SELECT mean("usage_idle") FROM "cpu"
WHERE time > now() - 1h
GROUP BY time(5m)
```

## Sending Data

### Send Logs to Loki
Personal project - Private use

Using Promtail:
```yaml
clients:
  - url: http://localhost:3100/loki/api/v1/push
```

Using curl:
```bash
curl -X POST http://localhost:3100/loki/api/v1/push \
  -H "Content-Type: application/json" \
  -d '{"streams": [{"stream": {"job": "test"}, "values": [["'"$(date +%s)"'000000000", "test log"]]}]}'
```

### Send Metrics to InfluxDB

```bash
# Write point
curl -X POST http://localhost:8086/write?db=metrics \
  --data-binary 'temperature,location=room1 value=23.5'
```

## Common Commands

```bash
# Start stack
docker-compose up -d

# Stop stack
docker-compose down

# View logs
docker-compose logs -f [service]

# Restart specific service
docker-compose restart grafana
```

## Troubleshooting

### Grafana Can't Connect to Data Sources

```bash
# Check network connectivity
docker-compose exec grafana ping loki
docker-compose exec grafana ping influxdb
```

### InfluxDB Permission Denied

```bash
# Fix volume permissions
sudo chown -R 1000:1000 ./influxdb_data
```

### Loki Logs Not Appearing

- Verify Promtail is sending logs
- Check Loki URL in Promtail config
- Examine Loki logs: `docker-compose logs loki`

## Security Notes

**Important**:
- Change default Grafana password
- Use strong InfluxDB credentials
- Restrict network access (firewall/VPN)
- Enable HTTPS in production
- Regular backups of volumes

## Use Cases

- Application monitoring
- Infrastructure monitoring
- Log aggregation and analysis
- Performance troubleshooting
- Real-time alerting

## Resources

- [Grafana Documentation](https://grafana.com/docs/)
- [Loki Documentation](https://grafana.com/docs/loki/)
- [InfluxDB Documentation](https://docs.influxdata.com/)

## License

Personal project - Private use
