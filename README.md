# Simple PKI

This is an easy self-signing PKI (Public Key Infrastructure) manager. It consists of a single Root
CA (Certificate Authority) that signs all child certificates.

## Workflow

For the sake of the simplicity, it abstracts away the concepts of sub-ca's and CSR, as well as
certificates renewal. You only have to deal with the two operations: `issuing` and eventually
`revoking`. If you need to renew a certificate, you should first revoke it, then issue a new one.

This program was made to store the data in the cloud, so you would only retrieve them when necessary
(i.e. issuing a new cert). So, ideally, the data is stored at `/dev/shm` and as soon as you create
the CA using `bootstrap`, you should use the `cloud-sync` to send them to the S3 bucket.

## Setup

The setup process consists in initializing your own CA. It can be done like so:

```sh
$ bin/cert-manager bootstrap
```

Then you will have a CA of your own. Before running this command you might want to change the
`conf/openssl/ca-req.conf` to comply with your own needs. More specifically, change the line with
the `crlDistributionPoints` pointing to where your CRL will be located at.

If you won't use CRL, just remove that line.

### Configuration

Open the `conf/config.env` file and change everything accordingly.

### Commands

* **bin/cert-manager clear** - Remove the whole DB! Just use it when you've pushed the changes
* **bin/cert-manager boostrap** - Starts a new CA (and delete the old one, if existent)
* **bin/cert-manager issue-cert** \<CERT_TYPE\> \<CN\> - Create a new certificate
                                    The types can be: vpn or host
* **bin/cert-manager revoke-cert** \<CN\> - Revoke a previously issued certificate
* **bin/cert-manager make-crl** - Update the CRL file
* **bin/cloud-sync push** - Push the changed files to the S3 bucket
* **bin/cloud-sync pull** - Pull the changed files from the S3 bucket

### Common issues

1. Error when trying to issue a certificate: `There is already a certificate for /CN=XYZ`

   That happens because the CA has already issued a currently valid certificate for this CN. What
   you could do is to revoke that certificate and then issue it again (renew).

## References

* [OpenSSL PKI Tutorial](https://pki-tutorial.readthedocs.io/en/latest/index.html)
* [caman](https://github.com/radiac/caman)

## License

This project is licensed under the BSD 3-Clause License - see the [LICENSE.md](LICENSE.md) file for
details.
