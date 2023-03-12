Ansible Role: TLS Cert from CSR
===============================

The goal of this role changes as it's used. Initially, the goal is to assist the
user in generating an OpenSSL key and certificate signing request ("CSR"). The
user may then use the CSR to obtain a signed certificate from a third party.
After the user has obtained a signed certificate, the goal of this role becomes
to install the certificate.

In order to accomplish these two goals, this role functions in different ways
depending on whether or not a certificate file has been provided on the local
system.


### One-host Limitation

This role imposes an arbitrary one-host at a time limitation. While it is
certainly possible to make minor adjustments to this role to allow this role to
be run on multiple hosts simultaneously (as is the norm with most Ansible
roles), it is the current opinion of the maintainer that simultaneously
generating unique keys, CSRs, and certs per host is probably not the intention
of the user. The one-host limitation may be removed if a user opens an issue and
provides a persuasive use case for running this role on multiple systems
simultaneously.


Requirements
------------

This role requires the
[`community.crypto` module](https://github.com/ansible-collections/community.crypto).

You may install the required module by running the following command from this
directory.

```sh
ansible-galaxy install -r requirements.yml
````

This role will install `python3-cryptography` on remote systems(s) using your
Linux distribution's repositories.


Using this Role
---------------

This role should be used in a two step process, with each step corresponding to
separate executions of this role.

**Step 1: Generate a CSR**<br>
When first running this role, a private key will be generated remotely and a
corresponding CSR will be saved locally.

Use the CSR to request a signed certificate from a third party. Once a signed
certificate has been obtained, the CSR may be deleted.

**Step 2: Install a Signed Certificate**<br>
After you have obtained a signed certificate, save it locally in a file system
location accessible to this role. This role will install the certificate
remotely.


Example Playbook
----------------

The following is an example of how this role may be used in a playbook.

```yaml
- hosts: all
  roles: 
    - role: tdworz.tls_cert_from_csr
      common_name: example.com
      subject_alternative_names:
        - DNS:example.com
        - DNS:*.example.com
      key_file_remote_path: /srv/tls/private/example.com.key
      cert_file_remote_path: /srv/tls/certs/example.com.crt
      csr_file_local_path: /home/user/temp/example.com.csr
      cert_file_local_path: files/example.com.crt
```


Role Variables
--------------

**common_name** (default: `example.com`)
: Traditionally, the commonName certificate field
  has been used for specifying a domain name. This approach has been superseded
  by [RFC 2818](https://datatracker.ietf.org/doc/html/rfc2818) which specifies
  Subject Alternative Names. Regardless, it is still normal to put an
  important or representative domain in this field.

**subject_alternative_names** (default: `[DNS:example.com, DNS:*.example.com]`)
: [Subject Alternative Names](https://en.wikipedia.org/wiki/Subject_Alternative_Name)
  are domains (and other values) associated with a certificate. All domains
  to be covered by a certificate should be listed. Be sure to include any domain
  specified in *common_name* as some browsers now ignore certificate
  `commonName` fields. Domains should be preceded by `DNS:`, as in the examples.

**key_file_remote_path** (default: `/srv/tls/private/server.key`)
: The remote path where a key file should be installed.

**cert_file_remote_path** (default: `/srv/tls/certs/server.crt`)
: The remote path where a certificate file should be installed.

**csr_file_local_path** (default: `/tmp/server.csr`)
: The local path where a certificate signing request should be saved.

**cert_file_local_path** (default: `files/server.crt`)
: The local path where a signed certificate will be made available by the user
  for the role to install.


Supported Linux Distributions
-----------------------------

The policy of this role is to support Linux distributions while they are
supported with public updates. Once a distribution's official public support
period ends, this role will no longer support the distribution.

| Distribution  | Support Period | Currently Supported |
|---------------|----------------|---------------------|
| AlmaLinux     | 10 years       | 8, 9                |
| CentOS Stream | 10 years       | 8, 9                |
| Debian        | 5 years        | 10, 11              |
| Fedora        | 1 year         | 36, 37              |
| Oracle Linux  | 10 years       | 8, 9                |
| Rocky Linux   | 10 years       | 8, 9                |
| Ubuntu        | 5 years        | 20.04, 22.04        |

Support for other distributions including Amazon Linux, Arch Linux, and OpenSUSE
are under consideration. CentOS 7 will not be supported.


Copyright & License
-------------------

Copyright ©2023 Tom Dworzanski (tdworz)

This program is free software: you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation, either version 3 of the License, or (at your option) any later
version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
this program.  If not, please see https://www.gnu.org/licenses/gpl-3.0.en.html.
