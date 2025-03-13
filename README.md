# Redis Setup on AWS EC2 with Docker

This document provides step-by-step instructions for setting up Redis on an AWS EC2 Ubuntu instance using Docker.

## System Requirements

Redis is lightweight, but consider these minimum specs:
- 1GB RAM
- 1GB free disk space
- Port 6379 open in security group

## Installation Steps

### 1. Pull the Redis Docker Image

```bash
sudo docker pull redis
```

### 2. Create a Volume for Data Persistence

```bash
sudo docker volume create redis-data
```

### 3. Run Redis Container with Recommended Settings

```bash
sudo docker run --name asset-redis \
  -p 6379:6379 \
  --restart always \
  --memory=200m \
  -v redis-data:/data \
  -d redis redis-server \
  --requirepass your_strong_password \
  --appendonly yes
```

This command:
- Creates a container named "asset-redis"
- Maps Redis port 6379 to the host
- Enables automatic restart on system reboot
- Limits memory usage to 200MB
- Mounts a persistent volume for data storage
- Runs in detached mode
- Sets a password for authentication
- Enables append-only file persistence

### 4. Verify Container Status

```bash
sudo docker ps -a
```

Look for "asset-redis" with "Up" status in the output.

## Testing Redis

### 1. Connect to Redis CLI

```bash
sudo docker exec -it asset-redis redis-cli
```

### 2. Authenticate

```
AUTH your_strong_password
```

### 3. Basic Operations Test

```
PING
SET testkey "Hello Redis"
GET testkey
```

Expected output:
- `PONG`
- `OK`
- `"Hello Redis"`

### 4. Exit Redis CLI

```
exit
```

## Persistence Test

### 1. Store Test Data

```bash
sudo docker exec -it asset-redis redis-cli
AUTH your_strong_password
SET persistent_test "This should survive restarts"
exit
```

### 2. Restart Container

```bash
sudo docker restart asset-redis
```

### 3. Verify Data Persists

```bash
sudo docker exec -it asset-redis redis-cli
AUTH your_strong_password
GET persistent_test
exit
```

Expected output: `"This should survive restarts"`

## Monitoring and Management

### Check Resource Usage

```bash
sudo docker stats asset-redis
```

### View Logs

```bash
sudo docker logs asset-redis
```

### Container Management

```bash
# Stop Redis
sudo docker stop asset-redis

# Start Redis
sudo docker start asset-redis

# Restart Redis
sudo docker restart asset-redis

# Remove container (data will be preserved if using volume)
sudo docker rm -f asset-redis
```

## Security Considerations

1. **Use strong passwords**: Change `your_strong_password` to a complex password
2. **Limit network access**: Configure AWS security groups to restrict access to port 6379
3. **Regular backups**: Implement a backup strategy for critical data

## Troubleshooting

### Connection Issues
- Verify the container is running
- Check if port 6379 is open in security groups
- Ensure you're using the correct password

### Performance Issues
- Monitor memory usage with `docker stats`
- Consider increasing memory limit if needed
- Check disk space with `df -h`

## Conclusion

Your Redis instance is now set up using Docker with security, persistence, and memory management configured. For production use, consider implementing monitoring and regular backups.

``` Suraj Kumar Karn ```
