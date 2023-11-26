Suppose 2 plan for backup

# 1- give backup from in odoo app:

install autobackups module . take backup and save backups for 5 days.

option: send backup from sftp to other host or gdrive


odoo app --> autobackup 5 days



# 2- take backup from cron job

in this strategy we take volume directory backup and save it for 2 days also take backup from container image and save it for 2 days.
copy this .sh file to cron.daily to perform daily backup.  then we have 2 .sh file one for folder backup and another for containers backup.

.sh ---> daily and triger on develop or upload prod
container
volume folders













