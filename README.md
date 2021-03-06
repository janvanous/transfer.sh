# transfer.sh [![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/dutchcoders/transfer.sh?utm_source=badge&utm_medium=badge&utm_campaign=&utm_campaign=pr-badge&utm_content=badge) [![Go Report Card](https://goreportcard.com/badge/github.com/dutchcoders/transfer.sh)](https://goreportcard.com/report/github.com/dutchcoders/transfer.sh) [![Docker pulls](https://img.shields.io/docker/pulls/dutchcoders/transfer.sh.svg)](https://hub.docker.com/r/dutchcoders/transfer.sh/) [![Build Status](https://travis-ci.com/dutchcoders/transfer.sh.svg?branch=master)](https://travis-ci.com/dutchcoders/transfer.sh)

Easy and fast file sharing from the command-line. This code contains the server with everything you need to create your own instance.

Transfer.sh currently supports the s3 (Amazon S3), gdrive (Google Drive) providers, and local file system (local).

## Disclaimer
This project repository has no relation with the service at https://transfer.sh that's managed by https://storj.io.
So far we cannot address any issue related to the service at https://transfer.sh.


## Usage

### Upload:
```bash
$ curl --upload-file ./hello.txt https://transfer.sh/hello.txt
```

### Encrypt & upload:
```bash
$ cat /tmp/hello.txt|gpg -ac -o-|curl -X PUT --upload-file "-" https://transfer.sh/test.txt
````

### Download & decrypt:
```bash
$ curl https://transfer.sh/1lDau/test.txt|gpg -o- > /tmp/hello.txt
```

### Upload to virustotal:
```bash
$ curl -X PUT --upload-file nhgbhhj https://transfer.sh/test.txt/virustotal
```

### Deleting
```bash
$ curl -X DELETE <X-Url-Delete Response Header URL>
```

## Request Headers

### Max-Downloads
```bash
$ curl --upload-file ./hello.txt https://transfer.sh/hello.txt -H "Max-Downloads: 1" # Limit the number of downloads
```

### Max-Days
```bash
$ curl --upload-file ./hello.txt https://transfer.sh/hello.txt -H "Max-Days: 1" # Set the number of days before deletion
```

## Response Headers

### X-Url-Delete

The URL used to request the deletion of a file. Returned as a response header.
```bash
curl -sD - --upload-file ./hello https://transfer.sh/hello.txt | grep 'X-Url-Delete'
X-Url-Delete: https://transfer.sh/hello.txt/BAYh0/hello.txt/PDw0NHPcqU
```

## Examples

See good usage examples on [examples.md](examples.md)

## Link aliases

Create direct download link:

https://transfer.sh/1lDau/test.txt --> https://transfer.sh/get/1lDau/test.txt

Inline file:

https://transfer.sh/1lDau/test.txt --> https://transfer.sh/inline/1lDau/test.txt

## Usage

Parameter | Description | Value | Env
--- | --- | --- | ---
listener | port to use for http (:80) | | LISTENER |
profile-listener | port to use for profiler (:6060) | | PROFILE_LISTENER |
force-https | redirect to https | false | FORCE_HTTPS
tls-listener | port to use for https (:443) | | TLS_LISTENER |
tls-listener-only | flag to enable tls listener only | | TLS_LISTENER_ONLY |
tls-cert-file | path to tls certificate | | TLS_CERT_FILE |
tls-private-key | path to tls private key | | TLS_PRIVATE_KEY |
http-auth-user | user for basic http auth on upload | | HTTP_AUTH_USER |
http-auth-pass | pass for basic http auth on upload | | HTTP_AUTH_PASS |
ip-whitelist | comma separated list of ips allowed to connect to the service | | IP_WHITELIST |
ip-blacklist | comma separated list of ips not allowed to connect to the service | | IP_BLACKLIST |
temp-path | path to temp folder | system temp | TEMP_PATH |
web-path | path to static web files (for development or custom front end) | | WEB_PATH |
proxy-path | path prefix when service is run behind a proxy | | PROXY_PATH |
proxy-port | port of the proxy when the service is run behind a proxy | | PROXY_PORT |
ga-key | google analytics key for the front end | | GA_KEY |
provider | which storage provider to use | (s3, storj, gdrive or local) |
uservoice-key | user voice key for the front end  | | USERVOICE_KEY |
aws-access-key | aws access key | | AWS_ACCESS_KEY |
aws-secret-key | aws access key | | AWS_SECRET_KEY |
bucket | aws bucket | | BUCKET |
s3-endpoint | Custom S3 endpoint. | | S3_ENDPOINT |
s3-region | region of the s3 bucket | eu-west-1 | S3_REGION |
s3-no-multipart | disables s3 multipart upload | false | S3_NO_MULTIPART |
s3-path-style | Forces path style URLs, required for Minio. | false | S3_PATH_STYLE |
storj-access | Access for the project | | STORJ_ACCESS |
storj-bucket | Bucket to use within the project | | STORJ_BUCKET |
basedir | path storage for local/gdrive provider | | BASEDIR |
gdrive-client-json-filepath | path to oauth client json config for gdrive provider | | GDRIVE_CLIENT_JSON_FILEPATH |
gdrive-local-config-path | path to store local transfer.sh config cache for gdrive provider| | GDRIVE_LOCAL_CONFIG_PATH |
gdrive-chunk-size | chunk size for gdrive upload in megabytes, must be lower than available memory (8 MB) | | GDRIVE_CHUNK_SIZE |
lets-encrypt-hosts | hosts to use for lets encrypt certificates (comma seperated) | | HOSTS |
log | path to log file| | LOG |
cors-domains | comma separated list of domains for CORS, setting it enable CORS | | CORS_DOMAINS |
clamav-host | host for clamav feature  | | CLAMAV_HOST |
rate-limit | request per minute  | | RATE_LIMIT |
max-upload-size | max upload size in kilobytes  | | MAX_UPLOAD_SIZE |

If you want to use TLS using lets encrypt certificates, set lets-encrypt-hosts to your domain, set tls-listener to :443 and enable force-https.

If you want to use TLS using your own certificates, set tls-listener to :443, force-https, tls-cert-file and tls-private-key.

## Development

Switched to GO111MODULE

```bash
go run main.go --provider=local --listener :8080 --temp-path=/tmp/ --basedir=/tmp/
```

## Build

If on go < 1.11
```bash
go get -u -v ./...
```

```bash
go build -o transfersh main.go
```

## Docker

For easy deployment, we've created a Docker container.

```bash
docker run --publish 8080:8080 dutchcoders/transfer.sh:latest --provider local --basedir /tmp/
```

## S3 Usage

For the usage with a AWS S3 Bucket, you just need to specify the following options:
- provider
- aws-access-key
- aws-secret-key
- bucket
- s3-region

If you specify the s3-region, you don't need to set the endpoint URL since the correct endpoint will used automatically.

### Custom S3 providers

To use a custom non-AWS S3 provider, you need to specify the endpoint as defined from your cloud provider.

## Storj Network Provider

To use the Storj Network as storage provider you need to specify the following flags:
- provider `--provider storj`
- storj-access _(either via flag or environment variable STORJ_ACCESS)_
- storj-bucket _(either via flag or environment variable STORJ_BUCKET)_

### Creating Bucket and Scope

In preparation you need to create a scope (or copy it from the uplink configuration) and a bucket.

To get started, download the latest uplink from the release page: https://github.com/storj/storj/releases

After extracting, execute `uplink setup`. The Wizard asks for Satellite to use, the API Key 
(which you can retrieve via the Satellite UI), as well as an Encryption Key.
Once the uplink is setup create the bucket using the following schema: 
`uplink mb sj://<BUCKET>` where <BUCKET> is your desired name.

Afterwards you can copy the SCOPE out of the configuration file of the uplink and then start the startup of the
transfer.sh endpoint. For enhanced security its recommended to provide both the scope and the bucket name as ENV Variables.

Example:
```
export STORJ_BUCKET=transfersh
export STORJ_ACCESS=<SCOPE>
transfer.sh --provider storj
```

## Google Drive Usage

For the usage with Google drive, you need to specify the following options:
- provider
- gdrive-client-json-filepath
- gdrive-local-config-path
- basedir

### Creating Gdrive Client Json

You need to create a Oauth Client id from console.cloud.google.com
download the file and place into a safe directory

### Usage example

```go run main.go --provider gdrive --basedir /tmp/ --gdrive-client-json-filepath /[credential_dir] --gdrive-local-config-path [directory_to_save_config] ```

## Contributions

Contributions are welcome.

## Creators

**Remco Verhoef**
- <https://twitter.com/remco_verhoef>
- <https://twitter.com/dutchcoders>

**Uvis Grinfelds**

## Maintainer

**Andrea Spacca**

## Copyright and license

Code and documentation copyright 2011-2018 Remco Verhoef.
Code released under [the MIT license](LICENSE).
