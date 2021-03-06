# Other Services

## AWS Opsworks

- Chef and Puppet are open source platforms and allow to perform server configuration automatically
- They work great with EC2 and on-premise VM
- AWS Opsworks = Managed Chef and Puppet
- Alternative to AWS SSM

### Chef and Puppet

- They help with managing configuration as code which helps in having consistent deployments
- Work on Linux/Windows
- We can automate: user accounts, cron jobs, ntp, packages, services, etc.

## Elastic Transcoder

- Convert media files (video + music) stored in S3 into various formats for tables, PCs, smartphones, TVs, etc.
- Features:
    - Optimize bite rate
    - Create thumbnails
    - Create watermarks
    - Create captions
    - DRM
    - Provides progressive download
    - Encryption
- 4 components of Elastic Transcoder:
    - Jobs: what does the work of the transcoder
    - Pipeline: queue that manages the transcoding job
    - Presets: templates for converting media from one format to another
    - Notifications
- Pay for what we use, scales automatically, fully managed