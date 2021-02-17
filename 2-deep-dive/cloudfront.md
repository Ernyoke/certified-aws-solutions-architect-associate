# AWS CloudFront

- CloudFront is a content delivery network (CDN)
- It improves read performance, content is cached at the edge locations (currently there are 216 edge locations globally)
- CloudFront also offers DDos protection, integration with Shield and integration with AWS WAF (Web Application Firewall)
- CloudFront allows to expose external HTTPS end-points and it also can talk to internal HTTPS back-ends

## CloudFront Origins

- The location of the data which gets distributed by CloudFront can be in:
    - S3 bucket:
        - Recommended for distributing files and caching them at the edge locations
        - It offers enhanced security with CloudFront **Origin Access Identity (OAI)**
        - CloudFront can be used as an ingress for uploading files to S3
    - Custom Origin (HTTP) which could be the following:
        - Application Load Balancer
        - EC2 instance
        - S3 website (must be enabled the static website functionality on the bucket)
        - Any other HTTP back-end

## CloudFront Geo Restriction

- CloudFront can restrict who can access the distribution based on geographic location
- It provides:
    - Whitelisting: allow users to access the content if they from countries which are on the approved list
    - Blacklisting: deny access for users from countries which are listen on the banned list
- The country is determined using 3rd party Geo-IP database

## CloudFront vs S3 Cross Region Replication

- CloudFront:
    - Global Edge network
    - Files are cached for a time period (TTL)
    - Recommended fro static content that must be available everywhere in the world
- S3 Cross Region Replication:
    - Must be configured for each region for which we want replication
    - Files are updated in near real time
    - Read only
    - Recommended for dynamic content that needs to be available at low-latency in few regions

## CloudFront Signed URL/Signed Cookies

- Used for distributing exclusive content to specific users
- When a signed cookie/URL is created, a policy needs to be attached, which should contain:
    - URL expiration date
    - IP Ranges which can access the data
    - Trusted signers (which AWS accounts can create a signed URL)
- Signed URL time to live:
    - For shared content (movie, music) we should make it short
    - Fro private content we can mae it last longer
- Signed URL: we can get access to individual files
- Signed Cookies: we can get access to multiple files

## CloudFront Signed URL vs S3 Pre-Signed URL

- CloudFront Signed URL:
    - Allows access to a path, no matter the origin
    - It is an account wide key-pair, only the root account can manage it
    - It can filter by IP, path, date, expiration
    - We can leverage all the CloudFront caching features
- S3 Pre-Signed URL:
    - It issues requests as the person whe pre-signed the URL
    - It uses the IAM key of the signing IAM principal
    - It has a limited lifetime
