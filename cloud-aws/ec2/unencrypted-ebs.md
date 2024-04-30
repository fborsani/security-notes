# Unencrypted EBS

## Cloudshell snapshot enumeration

```
aws ec2 describe-snapshots    #All
aws ec2 describe-snapshots --snapshot-ids <id>    #by snap id
aws ec2 describe-snapshots --filters "Name=volume-id,Values=<volume id>" #by volume id
```

Find unencrypted snapshots

```
aws ec2 describe-snapshots --filters "Name=encrypted,Values=false"
```

## Mount EBS in instance and access data

To access the snapshot we need to mount it in a controlled EC2 instance. After logging in execute the following steps.

Enumerate the current EC2 instance id, region and availability zone. See[ IMDS section](./#imds) for documentation.

Create a new volume from the target EBS. Save the volume id because we'll need it in the next step

```
aws ec2 create-volume --snapshot-id <target snapshot id> --volume-type gp3 --region <ec2 region> --availability-zone <ec2 az id>
```

Attach the volume to the EC2 instance

```
aws ec2 attach-volume --region us-east-1 --device /dev/sdh --instance-id <instance id> --volume-id <created volume id>
```

Verify that the volume has been mounted successfully and create a mount point. Even if the command succeeds it takes a while to attach the volume to the instance. If you don't see the new disk wait a couple of minutes

```
sudo fdisk -l    #we expect to see a new disk

sudo mkdir /snapshot-recovery
sudo mount /dev/nvme1n1 /snapshot-recovery
cd /snapshot-recovery
```
