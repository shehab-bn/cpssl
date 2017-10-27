## letsencrypt-cpanel 

This is a cPanel/WHM plugin for the [Let's Encrypt](https://letsencrypt.org/) client. This plugin uses Perl and the WHM API, and requires a server running cPanel and WHM on it.

This is only for Personal use. 

### VERSION
Version 1.0

### Requirements

- CentOS 7


### Installation

```
cd /usr/local/cpanel/3rdparty/bin/git clone https://github.com/shehab-bn/cpssl.git
cd letsencrypt-cpanel
./install.sh
```

If everything goes well, you will see a new icon in the `WHM >> Plugins` section. Existing certificates will be shown, and you will be able to register new SSL certificates for domains on the server that do not yet have SSL associated with it.

Any SSL certificates added will automatically attempt renewal. You should not need to manually renew the certificates.

### Upgrading
	
```
cd letsencrypt-cpanel
/usr/local/cpanel/3rdparty/bin/git pull
./upgrade.sh
```

### Uninstall
	
```
cd letsencrypt-cpanel
./uninstall.sh
```

## Troubleshooting

### Status: 400, Detail: DNS name does not have enough labels, Type: urn:acme:error:malformed

Some OpenVZ VPS providers don't let hostname survive a reboot the way it needs to. Start by seeing how your hostname is formatted for your system like this:

```
hostname
```

If you see `sub` instead of `sub.yourdomain.com`, then it's formatted incorrectly. To fix the issue, you can do the following to correct the hostname, lock down permissions, then reboot the system:

```
hostnamectl set-hostname sub.yourdomain.com
chattr +i /etc/hostname
reboot
```

`chattr` will ensure that even root cannot write to the file to change it. Even on reboot.

If for some reason you want to modify the file again in the future, you can do this:

```
chattr -i /etc/hostname
```

### 403 error: Authorizations for these names not found or expired

Let's Encrypt verifies domains via http using the pathname `.well-known` and the subfolder `acme-challenge`, if there are any rules in `.htaccess` that redirect this folder to https (or elsewhere) the verification will fail. To exclude rewrites for the `.well-known` folder place the following line to `.htaccess` in your **document root** directly under `RewriteEngine On`:

```
RewriteRule ^.well-known(.*)$ - [L,NC]
```

If the `.well-known` folder is requested (by Let's Encrypt) it doesn't process further rules and avoids any SSL redirection that happens below it.
