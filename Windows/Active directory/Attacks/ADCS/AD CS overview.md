To obtain a certificate from AD CS, clients go through a process called enrollment. The client generate a public-private key pair and place the public key in a certificate signing request (CSR) message along with other details such as the the subject of the certificate and the certificate template name. ***Clients then sign the CSR with their private key and send the CSR to CA server. 

The CA server checks if the client can request certificates. If so, it will issue a certificate by looking up the certificate template that was requested in the CSR. if the template is permissions allow authenticating this user, the CA generates certificate using the *blueprint* of the certificate template (e.g, EKUs, issuance requirements, .., ..) and using the other info that was supplied in the CSR. If everything checks out the CA will then sign the certificate using the CA's private key then returns it to the client.

![[Pasted image 20250721061318.png]]

some important EKUs with their OU:
![[Pasted image 20250721061858.png]]
these when present in the certificate, permit authentication to AD.

we can find them using `certipy`:
```bash
certipy find -u 'Administrator' -p 'P@ssw0rd' -dc-ip 10.10.111.10 -vulnerable

```

> [!NOTE]
  💡 : To find the SID and other attributes of a target user like 'administrator', you can use the command: `certipy account -u 'USERNAME' -p 'PASSWORD' -dc-ip 'DC_IP' -user 'administrator' read`
  
***
How it can be abused??


this is the normal flow:


```mermaid
sequenceDiagram
    participant User as Low-priv User
    participant CA as Certificate Authority
    participant KDC as Domain Controller (KDC)

    User->>CA: Step 1: Discover vulnerable cert template
    User->>CA: Step 2: Request certificate with DA UPN
    CA-->>User: Step 3: Receive .pfx certificate

    User->>User: Step 4: Extract cert and private key
    User->>KDC: Step 5: Send AS-REQ with cert (PKINIT)

    KDC->>KDC: Step 6: Extract public key from cert
    KDC->>KDC: Step 7: Verify signature (AuthPack)

    alt Signature is valid
        KDC-->>User: Step 8: AS-REP (returns TGT)
        Note over User: Now impersonating DA with TGT
    else Signature invalid
        KDC-->>User: Step 8: KRB-ERROR (pre-auth failed)
    end

```


---
This is what a tool like `certipy` would do: all are addressed [here](https://github.com/ly4k/Certipy/wiki/06-%E2%80%90-Privilege-Escalation)

```mermaid |
sequenceDiagram
    participant Attacker as Attacker (Certipy)
    participant CA as Certificate Authority
    participant KDC as Domain Controller (KDC)

    %% Certificate request phase
    Attacker->>CA: Step 1: Request certificate with Administrator UPN (ESC1)
    CA-->>Attacker: Step 2: Receive .pfx (cert + private key)

    %% Authentication using cert
    Note over Attacker: Step 3: Load .pfx in Certipy

    Attacker->>KDC: Step 4: Send AS-REQ with certificate (PKINIT)

    KDC->>KDC: Step 5: Extract public key and verify signature

    alt Signature valid
        KDC-->>Attacker: Step 6: AS-REP (TGT issued)
        Attacker->>KDC: Step 7: TGS-REQ (Request service ticket)
        KDC-->>Attacker: Step 8: TGS-REP (Service ticket issued)
        Note over Attacker: Step 9: Full impersonation of Administrator achieved
    else Signature invalid
        KDC-->>Attacker: Step 6: KRB-ERROR (Pre-auth failed)
    end

```




certipy will finaly give you:
1-  A `.ccache` file that contains a TGT for the impersonated user (e.g., Administrator).
2- The NTLM hash of that user (extracted from the TGT response).
***
