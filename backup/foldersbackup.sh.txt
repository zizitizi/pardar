#!/bin/bash

# Define backup directory and filename

BACKUP_DIR=/var/backups


FILENAME=backup_$(date +'%Y-%m-%d_%H-%M-%S').tar.gz


# Create backup directory if it doesn't exist

if [ ! -d "$BACKUP_DIR" ]; then
    mkdir -p "$BACKUP_DIR"
    echo "Backup directory created: $BACKUP_DIR"
fi



# Archive and compress folders

tar -czvf $BACKUP_DIR/$FILENAME /root/proj  /var/lib/docker/volumes/proj_odoo-db-data /var/lib/docker/volumes/proj_odoo-web-data

# Print success message

echo "Backup completed successfully in $(date +'%Y-%m-%d_%H-%M-%S'): $BACKUP_DIR/$FILENAME" >> $BACKUP_DIR/output.log


find /var/backups -name "backup_*.tar.gz" -type f -mtime +2 -exec rm -rf {} \;

