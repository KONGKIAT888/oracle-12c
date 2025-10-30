# Oracle Database 12c Docker Container

Docker container for Oracle Database 12c Release 1 Standard Edition 2 with automated setup and configuration.

## Overview

This project provides a complete Docker setup for Oracle Database 12c SE2, including:
- Automated Oracle Database installation
- Pre-configured database and pluggable database
- Health monitoring
- Volume persistence
- Easy configuration via environment variables

## Prerequisites

Before building this image, you need to download the Oracle Database 12c Release 1 Standard Edition 2 installation files:

1. [linuxamd64_12102_database_se2_1of2.zip](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
2. [linuxamd64_12102_database_se2_2of2.zip](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)

Place these files in the same directory as the Dockerfile.

## Quick Start

### Using Docker Compose (Recommended)

```bash
# Start the Oracle container
docker-compose up -d

# View logs
docker-compose logs -f

# Stop the container
docker-compose down
```

### Using Docker Build

```bash
# Build the image
docker build -t k8ngkiat/oracle-12c .

# Run the container
docker run -d \
  --name oracle \
  -p 1521:1521 \
  -p 5500:5500 \
  -e ORACLE_SID=ORCLCDB \
  -e ORACLE_PDB=ORCLPDB1 \
  -e ORACLE_PWD=12345 \
  -e ORACLE_CHARACTERSET=AL32UTF8 \
  -v oracle-data:/opt/oracle/oradata \
  k8ngkiat/oracle-12c
```

## Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ORACLE_SID` | `ORCLCDB` | Oracle System Identifier |
| `ORACLE_PDB` | `ORCLPDB1` | Pluggable Database name |
| `ORACLE_PWD` | `12345` | Database admin password |
| `ORACLE_CHARACTERSET` | `AL32UTF8` | Database character set |

### Ports

- **1521**: Oracle Database listener
- **5500**: Oracle Enterprise Manager Express

### Volumes

- `/opt/oracle/oradata`: Persistent storage for database files

## Connecting to the Database

### Using SQL*Plus

```bash
# Connect to the container
docker exec -it oracle bash

# Connect as SYSDBA
sqlplus sys/12345@localhost:1521/ORCLCDB as sysdba

# Connect to the pluggable database
sqlplus sys/12345@localhost:1521/ORCLPDB1 as sysdba
```

### Using External Database Tools

- **Hostname**: localhost
- **Port**: 1521
- **SID**: ORCLCDB
- **Service Name**: ORCLPDB1
- **Username**: sys
- **Password**: 12345
- **Role**: SYSDBA

## Default Users

| Username | Default Password | Privileges |
|----------|------------------|------------|
| sys | 12345 | SYSDBA |
| system | 12345 | DBA |
| pdbadmin | 12345 | PDB admin |

## Health Check

The container includes a health check that monitors the database status:

```bash
# Check container health
docker ps --format "table {{.Names}}\t{{.Status}}"

# View health check logs
docker inspect oracle | grep Health -A 10
```

## Project Structure

```
.
├── Dockerfile                 # Multi-stage Docker build configuration
├── docker-compose.yml         # Docker Compose configuration
├── db_inst.rsp              # Database installation response file
├── dbca.rsp.tmpl            # Database configuration assistant template
├── checkDBStatus.sh         # Database health check script
├── checkSpace.sh            # Disk space verification script
├── createDB.sh              # Database creation script
├── installDBBinaries.sh     # Database binaries installation script
├── installPerl.sh           # Perl installation script
├── runOracle.sh             # Main Oracle runtime script
├── runUserScripts.sh        # User scripts execution script
├── setPassword.sh           # Password configuration script
├── setupLinuxEnv.sh         # Linux environment setup script
└── startDB.sh               # Database startup script
```

## Important Notes

### First Startup

The first time you start the container, it will:
1. Install Oracle Database binaries (this takes 15-30 minutes)
2. Create the database and pluggable database
3. Configure the listener
4. Set up the admin passwords

Subsequent startups will be much faster as the database files are persisted in the volume.

### Security

- Change the default passwords in a production environment
- Use Oracle Wallet for secure credential management
- Enable database auditing as needed
- Regularly apply Oracle security patches

### Performance

- Allocate sufficient memory to Docker (minimum 4GB RAM recommended)
- Use SSD storage for better I/O performance
- Configure appropriate SGA and PGA parameters for your workload

## Troubleshooting

### Common Issues

1. **Insufficient Memory**: Ensure Docker has at least 4GB RAM allocated
2. **Disk Space**: Verify you have at least 15GB free space
3. **Installation Files**: Ensure Oracle installation files are present and not corrupted

### Logs

```bash
# View container logs
docker logs oracle

# View specific Oracle logs
docker exec -it oracle tail -f /opt/oracle/diag/rdbms/orcl/ORCLCDB/trace/alert_ORCLCDB.log
```

## License

This project is licensed under the UPL 1.0 license.

## Oracle License

Note that the Oracle Database software is subject to Oracle's license terms. Please ensure you have the appropriate licenses before using this in production.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## Support

For Oracle Database related issues, please refer to:
- [Oracle Documentation](https://docs.oracle.com/)
- [Oracle Support](https://support.oracle.com/)

For Docker and container related issues, please check the Docker documentation or create an issue in this repository.