# Using the Key Management Interoperability Protocol (KMIP)

Percona Server for MongoDB adds support for secure transfer of keys using the [OASIS Key Management Interoperability Protocol (KMIP)](https://docs.oasis-open.org/kmip/kmip-spec/v2.0/os/kmip-spec-v2.0-os.html). The KMIP implementation was tested with the [PyKMIP server](https://pykmip.readthedocs.io/en/latest/server.html) and the [HashiCorp Vault Enterprise KMIP Secrets Engine](https://www.vaultproject.io/docs/secrets/kmip).

KMIP enables the communication between key management systems and the database server. KMIP provides the following benefits:

* Streamlines encryption key management
* Eliminates redundant key management processes

Starting with version 4.4.15-15, you can specify multiple KMIP servers for failover. On startup, Percona Server for MongoDB connects to the servers in the order listed and selects the one with which the connection is successful.

Starting with version 4.4.16-16, the `kmipKeyIdentifier` option is no longer mandatory. When left blank, the database server creates a key on the KMIP server and uses that for encryption. When you specify the identifier, the key with such an ID must exist on the keystore.

Percona Server for MongoDB cannot encrypt existing data. If there is data in place, see the steps [how to encrypt existing data](https://www.mongodb.com/docs/v4.4/tutorial/configure-encryption/#std-label-encrypt-existing-data).

## KMIP parameters

| Option            | Type    | Description    |
| ----------------- | ------- | -------------- |
|`--kmipServerName` | string  | The hostname or IP address of the KMIP server. As of version 4.4.15-15, multiple KMIP servers are supported as the comma-separated list, e.g. `kmip1.example.com,kmip2.example.com`|
| `--kmipPort`      | number  | The port used to communicate with the KMIP server. When undefined, the default port `5696` will be used|
| `--kmipServerCAFile`| string| The path to the CA certificate file. CA file is used to validate secure client connection to the KMIP server|
| `--kmipClientCertificateFile`| string| The path to the PEM file with the KMIP client private key and the certificate chain. The database server uses this PEM file to authenticate the KMIP server|
| `--kmipKeyIdentifier`| string| Optional starting with version 4.4.16-16. The identifier of the KMIP key. If the key does not exist, the database server creates a key on the KMIP server with the specified identifier. When you specify the identifier, the key with such an ID must exist on the key storage. You can only use this setting for the first time you enable encryption|
| `--kmipRotateMasterKey`| boolean| Controls master keys rotation. When enabled, generates the new master key version and re-encrypts the keystore. Available as of version 4.4.14-14. Requires the unique --kmipKeyIdentifier for every mongod node |
| `--kmipClientCertificatePassword`| string| The password for the KMIP client private key or certificate. Use this parameter only if the KMIP client private key or certificate is encrypted. Available starting with version 4.4.15-15|

# Key rotation

Starting with release 4.4.14-14, the support for [master key rotation](https://www.mongodb.com/docs/manual/tutorial/rotate-encryption-key/#kmip-master-key-rotation) is added. This enables users to comply with data security regulations when using KMIP.

# Configuration

Make sure you have obtained the root certificate, and the keypair for the KMIP server and the `mongod` client. For testing purposes you can use the [OpenSSL](https://www.openssl.org/) to issue self-signed certificates. For production use we recommend you use the valid certificates issued by the key management appliance.

To enable data-at-rest encryption in Percona Server for MongoDB using KMIP, edit the `/etc/mongod.conf` configuration file as follows:

```yaml
security:
  enableEncryption: true
  kmip:
    serverName: <kmip_server_name>
    port: <kmip_port>
    clientCertificateFile: </path/client_certificate.pem>
    serverCAFile: </path/ca.pem>
    keyIdentifier: <key_name>
```

Alternatively, you can start Percona Server for MongoDB using the command line as follows:

```{.bash data-prompt="$"}
$ mongod --enableEncryption \
  --kmipServerName <kmip_servername> \
  --kmipPort <kmip_port> \
  --kmipServerCAFile <path_to_ca_file> \
  --kmipClientCertificateFile <path_to_client_certificate> \
  --kmipKeyIdentifier <kmip_identifier>
```
